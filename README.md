=================================================================
DIGITAL FORENSICS INVESTIGATION & TRIAGE REPORT
=================================================================
Case Reference: CIP-B104-CS3-C11_26_DFIT_17300
Evidence Items: 
    1. RHINOUSB - USB Flash Drive Image
    2. rhino2.pcapng - Network Traffic Capture
Investigator: Simon Friday Adeka
Date of Investigation: 04-05 September 2026
Location: Abuja, Nigeria

=================================================================
1.0 EXECUTIVE SUMMARY
=================================================================
This report documents the forensic acquisition, preservation, analysis, 
and findings relating to two evidence items submitted for case 
CIP-B104-CS3-C11_26_DFIT_17300. The examination was conducted in 
accordance with DFIT standards to ensure integrity, repeatability, 
and legal defensibility.

Evidence 1: RHINOUSB - USB storage device
Evidence 2: rhino2.pcapng - Network packet capture

Objectives:
1.  Forensically acquire and verify all evidence
2.  Analyze file system, network traffic, and recover deleted data
3.  Identify evidence of data hiding, C2 communication, or data exfiltration
4.  Document all actions with screenshots, hashes, and timelines

Key Findings:
1.  USB forensic image created and verified. SHA256 hashes match.
2.  USB device contains 1 FAT16 partition.
3.  Deleted file recovered: f0335017_She_died_in_February_at_the_age_of_74.doc
4.  Steganographic data identified in f0335081.jpg and extracted using 
    passphrase: "She died in February at the age of 74"
5.  Extracted content references events in August 2001.
6.  Network analysis identified HTTP data exfiltration from 
    137.30.123.234 to 64.233.167.104.
7.  File rhino4.jpg was exfiltrated at timestamp 0.346499 seconds.

=================================================================
2.0 ACQUISITION & VERIFICATION
=================================================================
To maintain integrity, all actions were performed on verified working copies.

2.1 Original Evidence Hashing
The original evidence files were hashed prior to analysis.
Figure 1: SHA256 manifest of RHINOUSB.dd and rhino2.pcapng

2.2 Partition & File System Analysis - RHINOUSB
The working image was analyzed to determine layout and file system.
Figure 2: mmls output showing 1 FAT16 partition for RHINOUSB_working.dd
Figure 3: fsstat output confirming FAT16 file system

=================================================================
3.0 ANALYSIS & RECOVERY - USB
=================================================================
3.1 File Listing
All files were listed, including deleted entries.
Figure 4: fls -r output showing all files including gumbo1.txt, gumbo2.txt
Figure 5: fls -d output showing deleted files including 
          f0335017_She_died_in_February_at_the_age_of_74.doc

3.2 Data Carving
PhotoRec was used to recover deleted files and carved images.
Figure 6: PhotoRec v7.2 initialized on evidence image
Figure 7: Recovered files listed. Key files: 
          f0335017_She_died_in_February_at_the_age_of_74.doc, f0335081.jpg

3.3 Timeline Analysis
Figure 8: mactime timeline.csv output

=================================================================
4.0 ANALYSIS & RECOVERY - NETWORK
=================================================================
4.1 Network Traffic Analysis
rhino2.pcapng was analyzed in Wireshark 4.2 to identify C2 communication 
and data exfiltration. Display filter applied: http.request or http.response

4.2 Key Network Findings
- Victim IP: 137.30.123.234
- C2/Exfil Server IP: 64.233.167.104
- Exfiltration Method: HTTP GET Request
- File Exfiltrated: rhino4.jpg
- Exfiltration Timestamp: 0.346499 seconds
- Total HTTP Requests: 19
- Source Port: 2024 -> Destination Port: 80

4.3 Timeline of Network Events
Time         Event                                    Source            Destination
0.000s    TCP 3-Way Handshake Initiated            137.30.123.234    64.233.167.104
0.165912s    Initial HTTP GET /                       137.30.123.234    64.233.167.104
0.346499s    DATA EXFILTRATION: GET /rhino4.jpg       137.30.123.234    64.233.167.104
0.347797s    HTTP GET /images/logo.gif                137.30.123.234    64.233.167.104

Figure 9: Wireshark HTTP Session Capture
          [File: 06_Screenshots/09_http_session.png]
          Shows HTTP traffic between victim and C2 server

Figure 10: Timeline Filtered - Data Exfiltration
           [File: 06_Screenshots/10_timeline_filtered.png]
           Shows GET /rhino4.jpg at 0.346499s

=================================================================
5.0 ADVANCED ANALYSIS
=================================================================
5.1 Steganography Examination - USB
Carved JPEG images were examined for hidden data using steghide and stegcracker.
Figure 11: steghide info and stegcracker run on f0335081.jpg
Figure 12: Keyword grep found "rhino" in .doc file. 
           Passphrase identified: "She died in February at the age of 74"

5.2 Evidence Extraction
Using the filename as a passphrase, data was successfully extracted from f0335081.jpg
 
Figure 13: Content of f0335017_She_died_in_February_at_the_age_of_74.doc
Figure 14: Extracted evidence displayed from extracted_evidence.txt
Figure 15: Creation and hashing of extracted_evidence.txt

Extracted Evidence Content:
=== EXTRACTED FROM f0335081.jpg USING PASSPHRASE: She died in February at the age of 74 ===
the age of 74. In August 2001 it wasn't a decision, since the alternative was regret. 
It wasn't her fault that I didn't go to the drugs... 
My mother and I have a unique relationship. Chasing Amy... 
4th of July... Wolves games...
 
SHA256 of Extracted Evidence: 2545063f0580a80936bd999b60b683d66e565c0004f84b28e9740afaaa87ae5b

5.3 Provenance Logging
All actions were logged to maintain chain of custody.
Figure 16: master_provenance.csv showing acquisition, hashing, and analysis steps

=================================================================
6.0 CONCLUSION
=================================================================
Relevant evidence was recovered from both evidence items. Chain of custody 
was maintained throughout.

USB Finding: 
Critical evidence identified - A steganographic JPEG file f0335081.jpg 
containing hidden text was recovered and decrypted using a passphrase found 
in a deleted document. The extracted text references dates and personal 
events that may be relevant to the investigation.

Network Finding:
Host 137.30.123.234 was compromised and used to exfiltrate data rhino4.jpg 
to external server 64.233.167.104 via HTTP at 0.346499s.

All findings were documented, hashed, and packaged. No anti-forensics detected.

Recommendation:
1.  USB: The extracted content requires further review by the case lead.
2.  NETWORK: Block 64.233.167.104 at firewall. Isolate and image host 137.30.123.234.
3.  Implement DLP and HTTP content monitoring for future exfil attempts.

=================================================================
7.0 APPENDIX
=================================================================
7.1 Evidence Hash Summary
File Name                                             MD5                                SHA256
f0335017_She_died_in_February_at_the_age_of_74.doc    68059d3355f0138c9fdd7eaa75e7bc16   95c06c8815cf4bc368a005b94958e34933720648351134929d9fbf7fe2100629
f0335081.jpg                                          [ADD]                              [ADD]
extracted_evidence.txt                                [ADD]                              2545063f0580a80936bd999b60b683d66e565c0004f84b28e9740afaaa87ae5b
rhino2.pcapng                                         [ADD]                              [ADD]

7.2 Screenshot Index
06_Screenshots/09_http_session.png     - Wireshark HTTP traffic
06_Screenshots/10_timeline_filtered.png - Exfiltration at 0.346499s

7.3 Final Submission Package
Folder: CIP-B104-CS3-C11_26_DFIT_17300_Simon_Friday_Adeka/
Figure 17: Final ZIP archive: CIP-B104-CS3-C11_26_DFIT_17300_SUBMISSION.zip

Analyst Signature: Simon Friday Adeka
Date: 05/09/2026
