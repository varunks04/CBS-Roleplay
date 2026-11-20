# Lab 04: Phishing Email Header and Body Analysis

[⬅️ Prev: Wireshark Lab](./03_Lab_Packet_Analysis_with_Wireshark.md) | [Back to Labs](./README.md) | [Next: Advanced Topics Track ➡️](../08_Advanced_Topics/README.md)

---

### Objective
Examine a raw phishing email (`.eml`), inspect the email transit hops, verify SPF/DKIM/DMARC authentication results, and use CyberChef to safely extract and defang embedded malicious indicators.

---

### Raw Phishing Email Sample Header

```text
Delivered-To: victim@company.com
Received: by 2002:a05:6512:481:: with SMTP id a1csp29482;
        Fri, 20 Nov 2026 06:14:10 -0800 (PST)
Return-Path: <spoofed-notifications@evil-spoofer.ru>
Received: from mail.evil-spoofer.ru (mail.evil-spoofer.ru. [194.26.29.88])
        by mx.google.com with ESMTPS id v18si294812
        for <victim@company.com>;
Received-SPF: fail (google.com: domain of spoofed-notifications@evil-spoofer.ru does not designate 194.26.29.88 as permitted sender) client-ip=194.26.29.88;
Authentication-Results: mx.google.com;
       dkim=fail header.i=@company.com header.s=selector1;
       spf=fail (google.com: domain of evil-spoofer.ru does not designate 194.26.29.88);
       dmarc=fail (p=QUARANTINE sp=QUARANTINE dis=QUARANTINE) header.from=company.com
From: "IT Helpdesk Support" <admin@company.com>
To: <victim@company.com>
Subject: URGENT: Your password expires in 1 hour. Click here to retain access.
```

---

### Step-by-Step Header Dissection

1. **`From:` vs `Return-Path:`**:
   - The display name says `"IT Helpdesk Support" <admin@company.com>`.
   - But the true envelope sender (`Return-Path`) is `<spoofed-notifications@evil-spoofer.ru>`.
2. **`Received-SPF: fail`**:
   - The sending server IP `194.26.29.88` is **NOT** authorized in the SPF DNS record for `company.com`.
3. **`dmarc=fail`**:
   - The email failed both SPF and DKIM alignment, proving the sender spoofed the corporate domain.

---

### Defanging Malicious URLs in CyberChef

Never send clickable malicious URLs in an email or ticket!
1. Open CyberChef (`https://gchq.github.io/CyberChef/`).
2. Paste the phishing URL: `http://login.secure-microsoft-verify.ru/login.php?user=victim@company.com`.
3. Add the **Defang URL** recipe.
4. Output generated:
   `hxxp[://]login[.]secure-microsoft-verify[.]ru/login[.]php?user=victim@company[.]com`
5. Paste this safe defanged indicator into your incident ticket.
