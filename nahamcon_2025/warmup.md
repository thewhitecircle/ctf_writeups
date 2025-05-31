# warmup

## The Oddyssey
> Solved by thewhiteh4t

This was a basic scripting challenge. It can be solved using other languages too, such as python, but I used bash as it was a more quick solution. When we connect to the given endpoint a chunk of text is received and a message to press enter to continue. After every chunk we were supposed to press enter and the next chunk of text is received.

So the idea was that we will get the flag either at the end of the text or somewhere in between. Initially I pressed enter manually few times, but it was like never ending, so I wrote the following infinite loop.


    > while true; do
        echo ''
        sleep 0.5
    done | nc challenge.nahamcon.com 32084

This is how we can automate enter key action in `netcat`, we just echo blank and use pipe to netcat. A delay is important as we want the next chunk to receive before pressing enter. The amount of delay depends on how fast the server is sending the response.

And below you can see the flag is hidden between text in a random chunk.


![](https://i.imgur.com/meAnptz.png)


This can be further improved by redirecting all the received text into a file so we can `grep` the flag later from the file, that way we don’t miss the flag.

**Key Learning and Takeaways**


- Automation is Your Best Friend : When it feels like something is never ending, that's your cue to stop manual labor and start scripting! Even a simple loop can save you a lot of time and frustration.
- The Importance of Delays : It's super important to match the server's response time. Even in real world testing delays are required so that we don’t affect the availability of the target.


----------


## Screenshot
> Solved by thewhiteh4t

In this challenge we got a screenshot of `hexdump` of a zip file. So I used an online OCR tool to extract the text from the image.

OCR stands for Optical Character Recognition. Alternatively we can write a python script using `tesseract` or `opencv` and achieve similar results locally.

For this challenge I used [OnlineOCR.net](https://www.onlineocr.net)


![](https://i.imgur.com/rfKxlsE.png)


After that I removed index and ASCII part as we only need the hex values to generate the file and fixed some typos like `l → 1` and `O → 0`


    504b 0304 3300 0100 6300 2f02 b55a 0000
    0000 4300 0000 2700 0000 0800 0b00 666c
    6167 2e74 7874 0199 0700 0200 4145 0300
    003d 42ff d1b3 5f95 0314 24f6 8b65 c3f5
    7669 f14e 8df0 003f e240 b3ac 3364 859e
    4c2d bc3c 36f2 d4ac c403 7613 85af e4e3
    f90f bd29 d91b 614b a2c6 efde 11b7 1bcc
    907a 72ed 504b 0102 3f03 3300 0100 6300
    2f02 b55a 0000 0000 4300 0000 2700 0000
    0800 2f00 0000 0000 0000 2080 b481 0000
    0000 666c 6167 2e74 7874 0a00 2000 0000
    0000 0100 1800 8213 8543 07ca db01 0000
    0000 0000 0000 0000 0000 0000 0000 0199
    0700 0200 4145 0300 0050 4b05 0600 0000
    0001 0001 0065 0000 0074 0000 0000 00

Then using `xxd` I converted the dump back to binary and get the zip file


    xxd -r -p <input_file> <output_file>


![](https://i.imgur.com/BO82p5C.png)


**Key Learning and Takeaways**


- Recovering files : Using `xxd` is like magic, it takes that raw `hexdump` and converts it right back into a usable binary file. It's a fundamental skill for reversing and forensics, letting you reconstruct files from their hex representation.
- Know your tools : While I used an online tool for OCR, it's cool to know you could also script something locally with Python libraries like Tesseract or OpenCV. It just goes to show how many different ways there are to solve a problem


----------


## Naham-Commencement 2025
> Solved by Legend

In this challenge there is a login page which we need to access to get the flag. Seeing the source code I found that the `username` and `passowrd` is in the `main.js`.


    function a(t) {
        let r = '';
        for (let i = 0; i < t.length; i++) {
            const c = t[i];
            if (/[a-zA-Z]/.test(c)) {
                const d = c.charCodeAt(0);
                const o = (d >= 97) ? 97 : 65;
                const x = (d - o + 16) % 26 + o;
                r += String.fromCharCode(x);
            } else {
                r += c;
            }
        }
        return r;
    }
    
    function b(t, k) {
        let r = '';
        let j = 0;
        for (let i = 0; i < t.length; i++) {
            const c = t[i];
            if (/[a-zA-Z]/.test(c)) {
                const u = c === c.toUpperCase();
                const l = c.toLowerCase();
                const d = l.charCodeAt(0) - 97;
                const m = k[j % k.length].toLowerCase();
                const n = m.charCodeAt(0) - 97;
                const e = (d + n) % 26;
                let f = String.fromCharCode(e + 97);
                if (u) {
                    f = f.toUpperCase();
                }
                r += f;
                j++;
            } else {
                r += c;
            }
        }
        return r;
    }
    
    function c(s) {
        return btoa(s);
    }
    
    document.addEventListener('DOMContentLoaded', function () {
        const x1 = "dqxqcius";
        const x2 = "YeaTtgUnzezBqiwa2025";
        const x3 = "ZHF4cWNpdXM=";
        const k = "nahamcon";
    
    
        const f = document.getElementById('loginForm');
        const u = document.getElementById('username');
        const p = document.getElementById('password');
        const s = document.getElementById('spinner');
        const d = document.getElementById('result');
    
        f.addEventListener('submit', function (e) {
            e.preventDefault();
    
            const q = u.value;
            const w = p.value;
    
    
            const q1 = a(q);
    
            const w1 = b(w, k);
    
            if (q1 !== x1 || w1 !== x2) {
                d.textContent = "Access denied. Client-side validation failed. Try again.";
                d.className = "error";
                d.style.display = "block";
                return;
            }
    
            s.style.display = "block";
            d.style.display = "none";
    
            const g = new FormData();
            g.append('username', q);
            g.append('password', w);
    
            fetch('/login', {
                method: 'POST',
                body: g
            })
                .then(h => h.json())
                .then(z => {
                    s.style.display = "none";
                    d.style.display = "block";
    
                    if (z.success) {
                        console.log("🎉 Server authentication successful!");
                        d.innerHTML = `
                        <p>${z.message}</p>
                        <p class="flag">🙌🎉${z.flag}🎉🙌</p>
                    `;
                        d.className = "success";
                    } else {
                        console.log("❌ Server authentication failed");
                        d.textContent = z.message;
                        d.className = "error";
                    }
                })
                .catch(err => {
                    console.error("🚨 Network error:", err);
                    s.style.display = "none";
                    d.style.display = "block";
                    d.textContent = "An error occurred while processing your request.";
                    d.className = "error";
                });
        });
    
    });

It is just that those are encrypted. Tried a bunch of algorithms, then figured it out `username` was encrypted with Caesar cipher and `password` was `Vigenère cipher`.


![](https://i.imgur.com/P90IFbu.png)

![](https://i.imgur.com/bYM8awB.png)


Putting them on the website we get the flag.


![](https://i.imgur.com/xGMELtt.png)


**Key Learning & Takeaways**

- Always check linked JavaScript files they often contain important logic or hidden data.
- Classic ciphers like `Caesar` and `Vigenère` are still common in CTFs; learn how to recognize and decode them.
- If credentials look encrypted, try simple cipher tools first before overthinking it.


----------


## Quartet
> Solve by thewhiteh4t

Four files are given with `.z01` extension, running `file` command on them shows the following output : 


    quartet.z01:  Zip multi-volume archive data, at least PKZIP v2.50 to extract
    quartet.z02:  data
    quartet.z03:  data
    quartet.z04:  Zip archive data, made by v3.0 UNIX, extract using at least v2.0, last modified, last modified Sun, May 10 2025 04:28:04, uncompressed size 2035495, method=deflate

Unzipping them leads to another file named `quartet.jpeg`. By running `strings` command on the image and grepping for flag string we can get the flag : 


    strings quartet.jpeg | grep flag
    flag{8f667b09d0e821f4e14d59a8037eb376}

**Key Learning & Takeaways**


- Run strings : You'd be surprised how often a flag or a crucial hint is just sitting there in plain text.
- Check the file type with file : A simple file command can tell you a lot about what you're dealing with. Knowing this helps you pick the right tools for the job.
- Keep an eye out for flag formats : Most CTFs use a consistent flag format (like `flag{...}`).


