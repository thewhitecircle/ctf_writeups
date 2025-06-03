---
layout: load_md
title: The White Circle | Nahamcon 2025 | Taken to School Writeup
desc: Check out our writeup for Taken to School for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: osint
challenge: Taken to School
tags: "osint, ava, log parsing, virustotal, python"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---


> Solved by avantika (@iamavu)

We are given a log file, which contains logs in the following format


    2024-12-22T13:50:15 CEF:0|PaloAltoNetworks|PAN-OS|10.5|35535|Trojan Signature Match|5|src=143.76.180.184 dst=192.168.117.75 spt=17345 dpt=443 proto=HTTPS act=detected fileName=syllabus_history101.pdf eventHash=7ad13c20d30a2a9fe0d2ecdf1d7b46d2
    2024-12-22T00:13:32 CEF:0|PaloAltoNetworks|PAN-OS|10.2|65282|Ransomware Signature Match|4|src=216.76.238.34 dst=192.168.115.221 spt=31880 dpt=443 proto=HTTPS act=blocked fileName=student_grades_q1.xlsx
    2024-12-22T07:49:45 CEF:0|Gigamon|GigaVUE|9.7|22445|Network Logon Attempt|5|src=130.142.140.231 dst=192.168.114.78 spt=64970 dpt=445 proto=SMB act=quarantined cs1Label=username cs1=student.ajackson cs2Label=password cs2=SpringBreak23 eventHash=92abbeeeec9c3844789bcee9ab72b2c3
    2024-12-22T00:10:43 CEF:0|Gigamon|GigaVUE|9.3|29961|Network Logon Attempt|9|src=109.56.138.63 dst=192.168.118.79 spt=47125 dpt=445 proto=SMB act=allowed cs1Label=username cs1=student.kpatel cs2Label=password cs2=LabReport#9 eventHash=1dc6d1ee184edc4d3c789f532db56715
    2024-12-22T09:18:48 CEF:0|Gigamon|GigaVUE|10.6|41903|Remote Desktop Logon|6|src=69.134.85.151 dst=192.168.112.11 spt=16805 dpt=3389 proto=TCP act=quarantined cs1Label=username cs1=coach.harris cs2Label=password cs2=Football!23 eventHash=c784094956103e601b4732561f085e61
    ......

Now, I endlessly thought as per the challenge description that it would be something to do with student hacking servers or something or maybe something suspicious along the lines(?)

but no, then it clicked to me its OSINT challenge, and we have IPs, and who knows everything malicious about IPs, that’s right, our good ol’ VT (VirusTotal)

Got its API key, asked claude-4 to write me a script to check for maliciousness of an IP, within the restrictions of the free API usage rate limits, it took about 125 min to run the complete script and gave me 5 malicious IPs out of which one was the correct one, so hit and trial for 5 IPs events hash and the IP being `91.218.50.11` gets us the flag -


`flag{5b16c7044a22ed3845a0ff408da8afa9}`

yipeee!

**Key Learning from the Challenge**

- **Challenge hinted at OSINT** — not just pattern matching, but external verification.
- Log entries included **public IPs**, which can be cross-referenced.
- Used **VirusTotal API** to check each IP’s reputation for malicious activity.
- Identified **5 suspicious IPs**, and from there isolated the one that matched the scenario.
- Submitted its `eventHash` as the flag.

Note - script to solve 


    #!/usr/bin/env python3
    """
    VirusTotal IP Bulk Checker with Rate Limiting
    Checks IP addresses for maliciousness using VirusTotal API v3
    Respects rate limits: 4 requests/min, 1000/day, 31K/month
    by claude <3
    """
    
    import requests
    import time
    import json
    import csv
    from datetime import datetime
    from typing import List, Dict, Optional
    import argparse
    import sys
    
    class VirusTotalIPChecker:
        def __init__(self, api_key: str):
            self.api_key = api_key
            self.base_url = "https://www.virustotal.com/api/v3/ip_addresses"
            self.headers = {"x-apikey": api_key}
            
            # Rate limiting configuration
            self.requests_per_minute = 4
            self.daily_quota = 1000
            self.monthly_quota = 31000
            
            # Tracking variables
            self.requests_made = 0
            self.daily_requests = 0
            self.start_time = time.time()
            self.last_request_time = 0
            
            # Results storage
            self.results = []
            
        def wait_for_rate_limit(self):
            """Ensure we don't exceed 4 requests per minute"""
            current_time = time.time()
            time_since_last = current_time - self.last_request_time
            
            # Wait at least 15 seconds between requests (4 per minute = 60/4 = 15 seconds)
            min_interval = 60 / self.requests_per_minute
            if time_since_last < min_interval:
                sleep_time = min_interval - time_since_last
                print(f"⏳ Rate limiting: waiting {sleep_time:.1f} seconds...")
                time.sleep(sleep_time)
            
            self.last_request_time = time.time()
        
        def check_quotas(self):
            """Check if we're approaching quota limits"""
            if self.daily_requests >= self.daily_quota:
                print("❌ Daily quota limit reached (1000 requests). Please wait until tomorrow.")
                return False
            
            if self.requests_made >= self.monthly_quota:
                print("❌ Monthly quota limit reached (31K requests). Please wait until next month.")
                return False
            
            # Warn when approaching limits
            if self.daily_requests >= self.daily_quota * 0.9:
                remaining = self.daily_quota - self.daily_requests
                print(f"⚠️ Warning: Only {remaining} daily requests remaining")
            
            return True
        
        def lookup_ip(self, ip_address: str) -> Optional[Dict]:
            """Look up a single IP address in VirusTotal"""
            if not self.check_quotas():
                return None
            
            self.wait_for_rate_limit()
            
            try:
                print(f"🔍 Checking {ip_address}... (Request {self.requests_made + 1})")
                
                # For API v3, IP is in the URL path
                url = f"{self.base_url}/{ip_address}"
                response = requests.get(url, headers=self.headers, timeout=30)
                
                self.requests_made += 1
                self.daily_requests += 1
                
                if response.status_code == 200:
                    data = response.json()
                    return self.parse_response(ip_address, data)
                elif response.status_code == 404:
                    print(f"ℹ️ No information available for {ip_address}")
                    return {"ip": ip_address, "status": "no_data", "malicious": False}
                elif response.status_code == 403:
                    print(f"❌ Authentication error for {ip_address}: Check your API key")
                    print(f"Response: {response.text}")
                    return {"ip": ip_address, "status": "auth_error", "error_code": 403}
                elif response.status_code == 429:
                    print("⚠️ Rate limit exceeded. Waiting longer...")
                    time.sleep(60)  # Wait 1 minute and retry
                    return self.lookup_ip(ip_address)
                else:
                    print(f"❌ Error checking {ip_address}: HTTP {response.status_code}")
                    print(f"Response: {response.text}")
                    return {"ip": ip_address, "status": "error", "error_code": response.status_code}
                    
            except requests.exceptions.RequestException as e:
                print(f"❌ Network error checking {ip_address}: {e}")
                return {"ip": ip_address, "status": "network_error", "error": str(e)}
        
        def parse_response(self, ip_address: str, data: Dict) -> Dict:
            """Parse VirusTotal v3 response and extract key information"""
            attributes = data.get("data", {}).get("attributes", {})
            
            result = {
                "ip": ip_address,
                "status": "success",
                "malicious": False,
                "suspicious": False,
                "harmless": 0,
                "malicious_count": 0,
                "suspicious_count": 0,
                "undetected": 0,
                "total_scans": 0,
                "detected_engines": [],
                "asn": attributes.get("asn", ""),
                "country": attributes.get("country", ""),
                "owner": attributes.get("as_owner", ""),
                "reputation": attributes.get("reputation", 0)
            }
            
            # Parse last_analysis_stats for v3 API
            stats = attributes.get("last_analysis_stats", {})
            if stats:
                result["harmless"] = stats.get("harmless", 0)
                result["malicious_count"] = stats.get("malicious", 0)
                result["suspicious_count"] = stats.get("suspicious", 0)
                result["undetected"] = stats.get("undetected", 0)
                result["total_scans"] = sum(stats.values())
                
                # Mark as malicious if any engines detected it
                if result["malicious_count"] > 0:
                    result["malicious"] = True
                elif result["suspicious_count"] > 0:
                    result["suspicious"] = True
            
            # Get detection details
            last_analysis_results = attributes.get("last_analysis_results", {})
            detected_engines = []
            for engine, details in last_analysis_results.items():
                if details.get("category") in ["malicious", "suspicious"]:
                    detected_engines.append(f"{engine}:{details.get('category')}")
            
            result["detected_engines"] = detected_engines
            
            # Check reputation score (negative is bad)
            if result["reputation"] < -10:
                result["suspicious"] = True
            
            return result
        
        def bulk_check(self, ip_list: List[str], output_file: str = None, resume_from: int = 0) -> List[Dict]:
            """Check multiple IPs with progress tracking and resume capability"""
            total_ips = len(ip_list)
            print(f"🚀 Starting bulk check of {total_ips} IP addresses")
            print(f"📊 Rate limits: {self.requests_per_minute}/min, {self.daily_quota}/day, {self.monthly_quota}/month")
            
            if resume_from > 0:
                print(f"📍 Resuming from IP #{resume_from}")
                ip_list = ip_list[resume_from:]
            
            estimated_time = (len(ip_list) * 15) / 60  # 15 seconds per IP
            print(f"⏱️ Estimated completion time: {estimated_time:.1f} minutes")
            print("-" * 60)
            
            for i, ip in enumerate(ip_list, start=resume_from):
                result = self.lookup_ip(ip)
                if result:
                    self.results.append(result)
                    
                    # Show status
                    if result.get("malicious") or result.get("suspicious"):
                        status_emoji = "🔴" if result.get("malicious") else "🟡"
                        print(f"{status_emoji} {ip} - FLAGGED")
                    else:
                        print(f"✅ {ip} - Clean")
                    
                    # Save progress periodically
                    if output_file and (i + 1) % 10 == 0:
                        self.save_results(output_file)
                        print(f"💾 Progress saved ({i + 1}/{total_ips})")
                else:
                    print(f"⏹️ Stopped at IP #{i} due to quota limits")
                    break
            
            if output_file:
                self.save_results(output_file)
            
            return self.results
        
        def save_results(self, filename: str):
            """Save results to CSV file"""
            if not self.results:
                print("No results to save")
                return
            
            with open(filename, 'w', newline='', encoding='utf-8') as csvfile:
                fieldnames = [
                    'ip', 'status', 'malicious', 'suspicious', 'malicious_count', 'suspicious_count',
                    'harmless', 'undetected', 'total_scans', 'asn', 'country', 'owner',
                    'reputation', 'detected_engines'
                ]
                writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
                writer.writeheader()
                
                for result in self.results:
                    # Convert lists to strings for CSV
                    if 'detected_engines' in result and isinstance(result['detected_engines'], list):
                        result['detected_engines'] = '; '.join(result['detected_engines'])
                    writer.writerow(result)
            
            print(f"💾 Results saved to {filename}")
        
        def generate_summary(self) -> Dict:
            """Generate summary statistics"""
            if not self.results:
                return {"error": "No results available"}
            
            total = len(self.results)
            malicious = sum(1 for r in self.results if r.get("malicious"))
            suspicious = sum(1 for r in self.results if r.get("suspicious"))
            clean = sum(1 for r in self.results if not r.get("malicious") and not r.get("suspicious"))
            errors = sum(1 for r in self.results if r.get("status") != "success")
            
            summary = {
                "total_checked": total,
                "malicious": malicious,
                "suspicious": suspicious,
                "clean": clean,
                "errors": errors,
                "malicious_percentage": (malicious / total * 100) if total > 0 else 0,
                "requests_made": self.requests_made
            }
            
            return summary
        
        def print_summary(self):
            """Print summary statistics"""
            summary = self.generate_summary()
            if "error" in summary:
                print(summary["error"])
                return
            
            print("\n" + "="*60)
            print("📊 SUMMARY REPORT")
            print("="*60)
            print(f"Total IPs checked: {summary['total_checked']}")
            print(f"🔴 Malicious: {summary['malicious']}")
            print(f"🟡 Suspicious: {summary['suspicious']}")
            print(f"✅ Clean: {summary['clean']}")
            print(f"❌ Errors: {summary['errors']}")
            print(f"🎯 Malicious rate: {summary['malicious_percentage']:.1f}%")
            print(f"📡 API requests used: {summary['requests_made']}")
            print("="*60)
    
    def load_ips_from_file(filename: str) -> List[str]:
        """Load IP addresses from a text file (one per line)"""
        try:
            with open(filename, 'r') as f:
                ips = [line.strip() for line in f if line.strip()]
            return ips
        except FileNotFoundError:
            print(f"❌ File not found: {filename}")
            sys.exit(1)
    
    def main():
        parser = argparse.ArgumentParser(description="Bulk IP maliciousness check using VirusTotal API")
        parser.add_argument("--api-key", required=True, help="VirusTotal API key")
        parser.add_argument("--input-file", required=True, help="File containing IP addresses (one per line)")
        parser.add_argument("--output-file", help="CSV file to save results (default: vt_results_TIMESTAMP.csv)")
        parser.add_argument("--resume-from", type=int, default=0, help="Resume from specific IP number (0-based)")
        
        args = parser.parse_args()
        
        # Load IP addresses
        print(f"📁 Loading IPs from {args.input_file}")
        ip_list = load_ips_from_file(args.input_file)
        print(f"✅ Loaded {len(ip_list)} IP addresses")
        
        # Set output file if not specified
        if not args.output_file:
            timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
            args.output_file = f"vt_results_{timestamp}.csv"
        
        # Initialize checker
        checker = VirusTotalIPChecker(args.api_key)
        
        try:
            # Perform bulk check
            results = checker.bulk_check(ip_list, args.output_file, args.resume_from)
            
            # Print summary
            checker.print_summary()
            
            print(f"\n✅ Analysis complete! Results saved to: {args.output_file}")
            
        except KeyboardInterrupt:
            print("\n\n⏹️ Process interrupted by user")
            checker.save_results(args.output_file)
            checker.print_summary()
            print(f"Partial results saved to: {args.output_file}")
    
    if __name__ == "__main__":
        main()

