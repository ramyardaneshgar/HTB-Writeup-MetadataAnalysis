# HTB-Writeup-MetadataAnalysis
HackTheBox Writeup: Automated web reconnaissance for metadata analysis.

By Ramyar Daneshgar 

⚠️ Disclaimer: All actions were performed in a controlled, authorized environment. Unauthorized use is strictly prohibited.


# Write-Up: Identifying Hidden Information Using Web Spidering and Metadata Analysis

Web reconnaissance is a critical skill in identifying valuable information about a target's infrastructure, configuration, and future plans. This write-up details my approach to identifying the location where specific reports would be stored on a target domain. The techniques and tools I employed here are universally applicable to a wide range of penetration testing and security auditing scenarios.

---

## Objective

The goal was to identify the storage location for future reports on the target domain using spidering, HTTP probing, and metadata analysis. I utilized tools like **Scrapy** and **curl** to enumerate resources and extract overlooked information embedded in the target website.

---

## Step 1: Setting Up Tools for Spidering and Probing

To automate crawling and analysis of the target domain, I used **Scrapy**, a Python-based web scraping framework, along with a spidering script, **ReconSpider**, specifically designed to uncover hidden resources and metadata.

- **Scrapy**: Enables high-speed crawling and parsing of web pages.
- **curl**: A versatile tool for making HTTP requests and examining headers and response codes.
- **jq**: Processes JSON output from spidering tools, making it easier to extract specific data.

### Commands used:
```bash
pip3 install scrapy
wget -O ReconSpider.zip https://example.com/path/to/ReconSpider.zip
unzip ReconSpider.zip
```

---

## Step 2: Spidering the Target Domain

I began by running the spidering script against the domain to enumerate available pages, resources, and links.

### Command:
```bash
python3 ReconSpider.py http://example.com
```

The spider crawled the target domain and recorded detailed logs, including:
- **HTTP request and response headers**
- **Redirect chains**
- **Page contents and metadata**

This process not only identified accessible resources but also helped uncover misconfigurations and development artifacts.

---

## Step 3: Probing Common Metadata Paths

Certain directories, like `.well-known`, are often used to store metadata about a domain’s configuration, security policies, or infrastructure. These directories adhere to standardized naming conventions and are frequently overlooked during deployment.

### Commands used:
```bash
curl -I http://example.com/.well-known/
curl -I https://example.com/.well-known/security.txt
curl -I https://example.com/.well-known/reports/
```

- **`.well-known/security.txt`**: Often contains contact details for reporting vulnerabilities.
- **`.well-known/reports/`**: Could house resources like logs, API information, or system configurations.

Even when direct information was unavailable, probing these paths provided insights into the server’s response behavior (e.g., `404 Not Found`, `301 Moved Permanently`).

---

## Step 4: Analyzing Metadata for Hidden Information

ReconSpider output a JSON file containing various artifacts from the crawl, including HTML comments, JavaScript files, and metadata tags. Analyzing these often-overlooked details yielded actionable information.

### To process and filter comments embedded in HTML:
```bash
cat results.json | jq '.comments'
```

Embedded comments in HTML frequently contain development notes, to-do lists, or deployment configurations left unintentionally in production environments. For example:
```html
<!-- TO-DO: change the location of future reports to reports.example-s3.amazonaws.htb -->
```

This comment directly revealed the location where future reports would be stored.

---

## Lessons Learned and General Applications

The techniques employed in this exercise are broadly applicable in penetration testing, security assessments, and infrastructure audits:

1. **Spidering for Discovery**: Automated tools like Scrapy allow for comprehensive crawling, enabling the discovery of hidden pages, endpoints, and files.
   - *Example*: Identify forgotten endpoints, admin panels, or backup files.

2. **Metadata Analysis**: HTML comments, metadata tags, and unreferenced scripts often reveal sensitive information about a system’s configuration, debugging notes, or future plans.
   - *Example*: Leverage comments like `<!-- API key: xxxxx -->` or `<!-- Staging URL: staging.example.com -->`.

3. **Standardized Directories**: Well-known directories (e.g., `.well-known`, `/admin`, `/backup`) often house configuration files or endpoint information that provide a starting point for further exploration.
   - *Example*: Discover a misconfigured `security.txt` that exposes unnecessary details.

4. **Understanding Response Behavior**: HTTP status codes and redirects provide insights into server configurations and potential hidden paths.
   - *Example*: A `301` redirect could point to a load balancer or a separate environment, such as staging or CDN.

---

## Conclusion

This methodology emphasizes the importance of combining spidering, metadata analysis, and common directory probing. By following a structured approach, I was able to uncover sensitive information that could be overlooked during standard scans. The techniques detailed here are invaluable for identifying misconfigurations, sensitive data exposure, and hidden system components across web domains.
```
