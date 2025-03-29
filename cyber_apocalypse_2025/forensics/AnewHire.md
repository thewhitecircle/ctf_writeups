---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | A new Hire Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: forensics
challenge: A new Hire
tags: "forensics, starry, email, eml"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>

## A new Hire
> Solved by Starry-Lord


    Return-Path: <elowan81@eldor.ia>
    Received: from mail-sor-f41.eldor.ia (mail-sor-f41.eldor.ia [209.85.220.41])
        by mx.eldor.ia (Postfix) with ESMTPS id A1B2C3D4E5F6
        for <work@eldor.ia>; Mon, 01 Jan 2024 12:34:56 -0700 (PDT)
        (version=TLS1.3 cipher=TLS_AES_256_GCM_SHA384 bits=256/256)
    Received: from [192.168.1.100] (customer-pool-192-168-1-100.eldor.ia [192.168.1.100])
        by mail-sor-f41.eldor.ia with ESMTPSA id F6E5D4C3B2A1
        for <work@eldor.ia>; Mon, 01 Jan 2024 12:34:55 -0700 (PDT)
        (version=TLS1.2 cipher=ECDHE-RSA-AES128-GCM-SHA256 bits=128)
    Authentication-Results: mx.eldor.ia;
        dkim=pass header.d=eldor.ia header.s=default header.b="3nw7H9qL1vXf5T8sK2zY6pB4rQ0eC7uM";
        spf=pass (mx.eldor.ia: domain of elowan81@eldor.ia designates 209.85.220.41 as permitted sender) smtp.mailfrom=elowan81@eldor.ia;
        dmarc=pass (p=quarantine sp=reject) header.from=eldor.ia
    DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=eldor.ia; s=default; t=1704123295;
        h=From:To:Subject:Date:Message-ID:MIME-Version:Content-Type;
        bh=Qm6qK81lr9XH/FGV5tS2kD3pW4aI7m2xZv9d1kE6cT8y=;
        b=V8pPz1n+R2tW4yZ6aB8cD0eF2gH4iJ6kL8mN0oP2qR4sT6uV8wX0yZ2aB4cD6eF8
         gH0iJ2kL4mN6oP8qR0sT2uV4wX6yZ8aB0cD2eF4gH6iJ8kL0mN2oP4qR6sT8uV0wX2yZ4;
    Received-SPF: pass (mx.eldor.ia: domain of elowan81@eldor.ia designates 209.85.220.41 as permitted sender) client-ip=209.85.220.41;
    ARC-Authentication-Results: i=1; mx.eldor.ia;
        dkim=pass header.d=eldor.ia header.s=default header.b="3nw7H9qL1vXf5T8sK2zY6pB4rQ0eC7uM";
        spf=pass smtp.mailfrom=elowan81@eldor.ia;
        dmarc=pass (p=quarantine sp=reject) header.from=eldor.ia
    ARC-Seal: i=1; a=rsa-sha256; t=1704123295; cv=none;
        d=eldor.ia; s=arc20240101;
        b=n8U2w3v1y7Z4x6T9r5q0p3o1l7m6k2j8i4h1g3f5d7s9a0b2c4e6r8t0y5u3w1x2
         a9b7c5d3e1f0g2h4i6j8k0l2m4n6o8p0q2r4s6t8u0v2w4x6y8z0A2B4C6D8E0F2G4;
    ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed;
        d=eldor.ia; s=arc20240101;
        h=From:To:Subject:Date:Message-ID:MIME-Version:Content-Type;
        bh=Qm6qK81lr9XH/FGV5tS2kD3pW4aI7m2xZv9d1kE6cT8y=;
        b=k4n5o6p7q8r9s0t1u2v3w4x5y6z7A8B9C0D1E2F3G4H5I6J7K8L9M0N1O2P3Q4R5
         S6T7U8V9W0X1Y2Z3a4b5c6d7e8f9g0h1i2j3k4l5m6n7o8p9q0r1s2t3u4v5w6x7y8;
    Message-ID: <20240101123456.1234567890@mail-sor-f41.eldor.ia>
    From: "Elowan" <elowan81@eldor.ia>
    To: "Work" <work@eldor.ia>
    Subject: Job Application - Resume Review 
    Date: Mon, 01 Jan 2024 12:34:56 -0700
    MIME-Version: 1.0
    Content-Type: text/plain; charset="UTF-8"
    Content-Transfer-Encoding: 7bit
    
    Hello Work Team,
    
    I hope this email finds you well. We have received a new application for the open position, and we wanted to bring it to your attention.
    
    The applicant, Lord Malakar, has an extensive background in leadership, strategic planning, and resource management. 
    With years of experience commanding large-scale operations, overseeing tactical deployments, and influencing key stakeholders, Malakar believes he would be a strong asset to your organization.
    
    Key Highlights from His Experience:
    
    Strategic Leadership: Spearheaded large-scale initiatives that reshaped industry landscapes.
    Crisis Management: Adept at handling high-pressure situations and making decisive calls.
    Team Motivation: Known for fostering loyalty and rallying teams toward ambitious goals.
    Innovative Thinking: Developed groundbreaking methods to enhance efficiency and control.
    We believe Malakar’s skills and experience could be a great fit for your team, and he is eager to discuss how he can contribute to [Company Name]’s continued success.
    
    You can review his resume here:
    `storage.microsoftcloudservices.com:[PORT]/index.php`
    
    Please let us know if you would like to proceed with the next steps in the hiring process.
    
    Best regards,
    Elowan
    
    PS: Make sure you replace the '[PORT]' with your instance's port. Additionally, make sure that any hostnames that are found point to your instance's IP address!
    

Visit the URL, press the button to see nothing happens, and find the script:


![](https://i.imgur.com/Eo7EL5m.png)


Visit http://docker-ip:port/3fe1690d955e8fd2a0b282501570e1f4/resumesS/resume_official.pdf
for Malakar’s Resume

then http://94.237.63.165:30920/3fe1690d955e8fd2a0b282501570e1f4/configs/client.py
for the flag:


![](https://i.imgur.com/ECso4Yu.png)

    HTB{4PT_28_4nd_m1cr0s0ft_s34rch=1n1t14l_4cc3s!!}


