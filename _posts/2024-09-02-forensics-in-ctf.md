---
layout: post
title:  "Forensics CTF - Tập tành làm thám tử trên không gian mạng"
tags: [blog,forensics]
authors: [5]
image: /assets/TungPost/Forensic_in_CTF/cover.webp
---

## 0x1 Lời mở đầu

---

Chào các bạn! Cuối cùng cũng đến đợt nghỉ lễ rồi. Nhân dịp rảnh rỗi này, mình muốn viết một cái gì đó để lại cho thế hệ sau. Người ta thường nói, điều quan trọng nên đặt lên hàng đầu. Vậy nên mình nghĩ, blog đầu tiên của mình sẽ viết về thứ quan trọng và hữu ích nhất trong con đường học vấn của mình, đó là CTF. Trong blog này, mình sẽ chủ yếu nói về CTF trong lĩnh vực Digital Forensics and Incident Response (DFIR).

![pic](/assets/TungPost/Forensic_in_CTF/GetReady.png)

## 0x2 CTF là gì?

---

### Định nghĩa

CTF (viết tắt của Capture The Flag) là một dạng thi đấu trong lĩnh vực an ninh mạng, nơi mọi người phải giải quyết các thử thách. Những thử thách này có thể thuộc nhiều lĩnh vực khác nhau như web, reverse, forensics,…. Mục tiêu là tìm "cờ" (flag) – thường là một chuỗi ký tự đặc biệt – được ẩn trong các thử thách.

![pic](/assets/TungPost/Forensic_in_CTF/flag_result.png)

                                      Flag được giấu trong 3D Object

### Các kiểu chơi

Hai kiểu chơi phổ biến nhất trong các cuộc thi CTF là **Jeopardy** và **Attack and Defense**

Kiểu chơi **Jeopardy** thường là một danh sách các thử thách có độ khó khác nhau và các câu trả lời được xác định rõ ràng. Người chơi được cung cấp một số loại tệp hoặc bằng chứng để phân tích, sau đó phải tìm “cờ” cho câu hỏi và nhập nó ở định dạng thích hợp để nhận được điểm. Nói tóm lại thì đây là kiểu chinh phục từng thử thách riêng lẻ.

![pic](/assets/TungPost/Forensic_in_CTF/jeopardy.png)

                                      Kiểu chơi Jeopardy

Kiểu chơi **Attack and Defense** phổ biến hơn khi muốn mô phỏng tình huống thực chiến. Các đội vừa đóng vai kẻ tấn công, vừa là người phòng thủ. Để ghi điểm, người chơi phải tấn công máy chủ hoặc dịch vụ của đối thủ đồng thời bảo vệ hệ thống của mình khỏi các cuộc tấn công. Điểm được tính dựa trên thời gian chiếm giữ máy chủ, dịch vụ, hoặc khi lấy được các tệp cụ thể từ máy chủ của đối phương.

![pic](/assets/TungPost/Forensic_in_CTF/attackDefense.png)

                                      Kiểu chơi Attack and Defense

Trong blog này thì mình sẽ tập trung nói về kiểu chơi Jeopardy do đó là kiểu chơi thông dụng hơn.

## 0x3 Tại sao lại nên chơi CTF?

---

### Tinh thần thể thao

Chơi CTF giống như tham gia các môn thể thao cạnh tranh như bóng rổ hay bóng đá. Việc tranh đua trên bảng xếp hạng mang lại sự phấn khích mà ít phương pháp học nào khác có thể sánh được. Cảm giác trở thành người đầu tiên giải được một thử thách sẽ tiếp thêm động lực để bạn vượt qua những thách thức tiếp theo.

### “Lợi ích”

Mình đặt từ "lợi ích" trong dấu nháy kép vì nhiều người hiểu nó chỉ là phần thưởng tiền mặt khi đạt giải cao trong cuộc thi. Đó đúng là một sự đền đáp xứng đáng, nhưng nó không miêu tả hết ý nghĩa của từ "lợi ích" mà mình muốn nói. Lợi ích thật sự chính là kiến thức và trải nghiệm bạn nhận được từ việc chơi CTF. Ví dụ, mình đã từng tham gia một cuộc thi mà mình chưa biết cách điều tra memory dump. Ít nhất sau giải đấu đó, mình đã học được những điều cơ bản về cách phân tích và những kỹ thuật mới để áp dụng trong các tình huống thực tế sau này.

![pic](/assets/TungPost/Forensic_in_CTF/Albert_Einstein.png)

Những kiến thức bạn thu được từ “thực hành” chắc chắn sẽ giúp ích cho bạn trong tương lai, đó chỉ là vấn đề thời gian. Nghiêm túc mà nói, bạn không biết những gì bạn không biết. Không quan trọng bạn đạt được 10 điểm hay 1000 điểm, miễn là bạn học được điều gì đó mới và cảm thấy vui vẻ khi làm điều đó, đó mới là điều quan trọng

## 0x4 Giới thiệu mảng Forensics trong CTF

---

Các thử thách CTF trong mảng forensics cũng giống như trong đời thực, bất kì loại thiết bị nào cũng có thể được đưa ra để điều tra. Trong bối cảnh dữ liệu trên các thiết bị ngày càng phát triển và mở rộng, càng có nhiều nơi để tìm kiếm manh mối nhằm giải quyết vấn đề. Trong các cuộc thi CTF, bạn sẽ có cơ hội phân tích bằng chứng từ:

- Máy tính Windows
- Máy tính MacOS
- Memory/RAM dump
- Disk Image
- Android dump
- Network (PCAP/Netflow/Snort logs)
- Malware samples

Bạn có thể thấy từ danh sách trên rằng bạn sẽ phải phân tích rất nhiều loại bằng chứng. Trong các cuộc thi khác mà mình đã tham gia, mình cũng gặp các tài nguyên cloud do việc sử dụng cloud đang dần trở nên phổ biến hơn. Bên cạnh đó, mình cũng đã thấy một số thử thách dựa trên mật mã nhiều hơn, trong đó bạn sẽ làm việc với các mật mã và hàm băm khác nhau để xác định câu trả lời. Bởi vậy nên kĩ năng dịch ngược (reverse) và hiểu biết về các kiểu mã hoá (crypto) là không thể thiếu.

![pic](/assets/TungPost/Forensic_in_CTF/crypto.png)

                                      Thử thách yêu cầu giải mã

## 0x5 Một số nơi để luyện tập

---

Vậy đâu là nền tảng chơi CTF tốt để bạn có thể mài giũa kĩ năng của mình?. Sau đây là một số nền tảng mà mình thấy phù hợp với người mới bắt đầu

- [TAS CTF](https://ctf.tlualgosec.com/challenges)

![pic](/assets/TungPost/Forensic_in_CTF/TASCTFpage.png)

                                      Trang chủ TAS CTF

Đây là trang web CTF của câu lạc bộ **TAS - TLU Algorithm and Security**. Trang web chứa rất nhiều thử thách thú vị do các thành viên trong Ban chuyên môn của câu lạc bộ sưu tập và tạo ra. Khi chơi các thử thách trên trang web này, các bạn có thể liên hệ trực tiếp với các thành viên câu lạc bộ để xin gợi ý hoặc trợ giúp. 

- [PicoCTF](https://play.picoctf.org/practice)
    
    ![pic](/assets/TungPost/Forensic_in_CTF/picoCTF_page.png)
    
                                      Trang chủ PicoCTF
    
    Đây là một trang web CTF có các thử thách trải dài từ mức độ rất dễ → khó phù hợp với người mới bắt đầu
    
- [TryHackMe](https://tryhackme.com/)
    
    ![pic](/assets/TungPost/Forensic_in_CTF/THM_page.png)
    
                                      Trang chủ TryHackMe
    
    Kiểu chơi thử thách trên website này sẽ khác một chút. Thử thách sẽ được chia thành các câu hỏi nhỏ. Việc của bạn là trả lời từng câu hỏi nhỏ đấy và các câu hỏi sẽ dẫn bạn từng bước tiến đến mục tiêu lớn hơn. Các bạn có thể tham khảo chơi các thử thách trong [link này](https://github.com/migueltc13/TryHackMe) trên TryHackMe
    
- [Cyber Defenders](https://cyberdefenders.org/blueteam-ctf-challenges/)
    
    ![pic](/assets/TungPost/Forensic_in_CTF/CyberDefenders.png)
    
                                      Trang chủ Cyber Defenders
    
    Các thử thách trên trang này sẽ mang độ khó và thực tế cao hơn rất nhiều so với những trang CTF phía trên. Mình liệt kê nó ở đây để nếu có bạn nào thật sự muốn thử sức thì có thể trải nghiệm.
    
- Một số trang web khác
    - [AboutDFIR](https://aboutdfir.com/education/challenges-ctfs/)
    - [HackTheBox](https://www.hackthebox.com/)
    - [CTF Time](https://ctftime.org/)
    - [PortSwigger](https://portswigger.net/web-security/all-labs)
    - [Dreamhack](https://dreamhack.io/)
    - [BUU CTF](https://buuoj.cn/)

## 0x6 Lời khuyên cho việc chơi CTF

---

Mình đã được hỏi rất nhiều lần rằng “Làm sao để chơi CTF tốt?”. Đó thực sự là một câu hỏi hóc búa và mình tự thấy bản thân mình cũng chưa đủ giỏi để có thể trả lời được câu hỏi đó. Nhưng mình nghĩ mình có thể chia sẻ một chút về quan điểm của mình về việc “Làm sao để chơi CTF tốt?”.

### Chuẩn bị kĩ càng

Giai đoạn đầu tiên và cũng là giai đoạn quan trọng nhất đó là giai đoạn chuẩn bị. Cũng như mọi việc trong cuộc sống, việc chuẩn bị trước cho “trận chiến” luôn luôn hữu ích. Biết được điều gì sắp xảy ra sẽ giúp ích cho quá trình chơi của bạn. Nếu bạn biết người tạo ra thử thách CTF đó là ai, hãy thăm dò họ. Tìm kiếm thông tin của họ trên các trang mạng xã hội. Đôi khi, họ sẽ đưa ra các gợi ý theo một hình thức nào đó, có thể là một webinar mà họ chia sẻ hoặc một blog, bài nghiên cứu về một lỗ hổng nào đó mà họ mới công khai. Tuy nhiên, đừng quá lạm dụng nó, đó có thể là một cái bẫy tác giả bày ra. Bạn cũng có thể xem lại các thử thách CTF mà họ đã từng tạo ra trước đó, để ý xem xu hướng ra đề của họ và họ thường hay giấu cờ ở vị trí nào. Đây là lý do chúng ta nên tự viết write-ups cho các thử thách mà mình từng chơi.

![pic](/assets/TungPost/Forensic_in_CTF/i-dont-know-who-you-are-but-i-will-use-osint-and-will-find-you.jpg)

### **Hãy tìm cho mình những người đồng đội tốt**

Luật lệ của mỗi giải CTF là khác nhau, nhưng hầu hết các giải sẽ cho phép các bạn chơi theo đội. Kiến thức từ nhiều người thông thạo các mảng khác nhau sẽ san sẻ khối lượng công việc mà bạn phải đảm nhiệm. Bạn có thể giải quyết các thử thách ở mảng forensic và crypto, reverse nhưng bạn không thể tự mình làm hết mọi việc. Hãy kiếm cho mình những người đồng đội ăn ý. Đảm bảo phối hợp chặt chẽ với đồng đội của bạn để không lãng phí thời gian để trả lời cùng một câu hỏi. Những người đồng đội cũng sẽ giúp bạn có động lực để chơi CTF hơn.

![pic](/assets/TungPost/Forensic_in_CTF/teamwork.jpg)

### **Hãy chuẩn bị trước đầy đủ “vũ khí”**

![pic](/assets/TungPost/Forensic_in_CTF/weapon.jpg)

Mình là một người dùng hệ điều hành Windows nên mình không thể đưa ra các công cụ có chức năng tương tự cho các bạn dùng hệ điều hành Linux hoặc Mac. Sau đây là một số công cụ miễn phí phục vụ cho việc điều tra, phân tích các loại chứng cứ mà mình yêu thích:

- **Phân tích chung**
    - [Autopsy](https://www.autopsy.com/)
    - [Bulk Extractor](https://github.com/simsong/bulk_extractor)
    - [DB Browser for SQLite](https://sqlitebrowser.org/dl/)
    - [FTK Imager](https://www.exterro.com/ftk-imager)
    - [Hindsight](https://dfir.blog/hindsight/)
- **Chromebook**
    - [cLEAPP](https://github.com/markmckinnon/cLeapp)
- **Ciphers**
    - [CyberChef](https://gchq.github.io/CyberChef/)
    - [dcode.fr](http://dcode.fr)
- **Google Takeout / Returns**
    - [RLEAPP](https://github.com/abrignoni/RLEAPP)
- **Mac**
    - [mac_apt](https://github.com/ydkhatri/mac_apt)
    - [plist Editor - iCopyBot](http://www.icopybot.com/plist-editor.htm)
- **Malware/PE**
    - [PEStudio](https://www.winitor.com/)
    - [PPEE (puppy)](https://www.mzrst.com/)
    - [IDA Pro](https://hex-rays.com/IDA-pro/)
    - [Binary Ninja](https://binary.ninja/)
    - [dnpsy](https://github.com/dnSpy/dnSpy)
- **Memory/RAM**
    - [MemProcFS](https://github.com/ufrisk/MemProcFS)
    - [Volatility 3](https://github.com/volatilityfoundation/volatility3)
    - [Volatility 2](https://github.com/volatilityfoundation/volatility)
- **Mobile Devices**
    - [ALEAPP](https://github.com/abrignoni/ALEAPP)
    - [Andriller](https://github.com/den4uk/andriller)
    - [APOLLO](https://github.com/mac4n6/APOLLO)
    - [ArtEX](https://www.doubleblak.com/software.php?id=8)
    - [iBackupBot](http://www.icopybot.com/itunes-backup-manager.htm)
    - [iLEAPP](https://github.com/abrignoni/iLEAPP)
- **Networks**
    - [Wireshark](https://www.wireshark.org/)
    - [NetworkMiner](https://www.netresec.com/?page=NetworkMiner)
- **Windows Analysis**
    - [Eric Zimmerman tools](https://ericzimmerman.github.io/#!index.md) / [KAPE](https://github.com/EricZimmerman/KapeFiles)
    - [USB Detective](https://usbdetective.com/)
- **Office Documents**
    - [oletools](https://github.com/decalage2/oletools), [oledump](https://blog.didierstevens.com/programs/oledump-py/)
    - [PDFTools](https://github.com/ropensci/pdftools)
- **Steganography**
    - [Aperi'Solve](https://www.aperisolve.com/)
    - [zsteg](https://github.com/zed-0xff/zsteg), [steghide](https://steghide.sourceforge.net/), [stegsolve](https://github.com/Giotino/stegsolve), ..

## **Nên làm gì khi cuộc thi đang diễn ra**

Đây là những điều bạn cần lưu ý khi đang chơi một giải CTF.

Đọc tiêu đề của câu hỏi một cách thật cẩn thận. Đôi lúc trong câu hỏi sẽ có gợi ý cho bạn. Ví dụ, “**Fetch** the run time of XXX application.”. Câu hỏi này gợi ý bạn nên điều tra các tệp Prefetch. Các câu hỏi thường sẽ cho bạn biết định dạng của câu trả lời. Bạn nên để ý vì nếu không có thể bạn sang sử dụng sai timestamp do chênh lệch múi giờ.

Một điều có vẻ ngớ ngẩn nhưng có thể hữu ích đó là chỉ cần đặt câu hỏi. Nếu bạn bối rối ở một câu hỏi, đừng ngần ngại nói chuyện với ban ra đề (nếu có thể), họ có thể dẫn bạn đi theo hướng mà bạn không nghĩ tới, trước khi bạn đi quá sâu vào một con đường không có đích đến.

![pic](/assets/TungPost/Forensic_in_CTF/takeAHint.png)

Một vài cuộc thi CTF có hệ thống gợi ý. Nếu chúng không ảnh hưởng gì đến số điểm của bạn thì hãy tận dụng chúng! Việc phân thắng bại dựa theo người nào dùng ít gợi ý hơn rất ít khi xảy ra. Nếu việc nhận gợi ý tốn điểm, bạn sẽ cần cân nhắc ưu và nhược điểm của việc không hoàn thành một thử thách có điểm cao hay là mất điểm để mua gợi ý đó. Nếu bạn mua gợi ý nhưng vẫn không làm được thử thách thì đó có thể là bước lùi lớn trong vị trí của bạn trên bảng xếp hạng.

## **Chiến thuật**

Có rất nhiều chiến thuật mà bạn có thể sử dụng để giải quyết các thử thách. Thông thường, các thử thách sẽ được chia thành các lĩnh vực khác nhau dựa theo loại bằng chứng được cung cấp cho người chơi ví dụ như Mobile/ Computer/ Network/ Hunt. Một số người thích hoàn thành tất cả các câu hỏi trong một lĩnh vực cụ thể trước khi chuyển qua lĩnh vực khác. Ví dụ, bạn thực sự giỏi trong việc điều tra Mobile, bắt đầu với những câu hỏi thuộc lĩnh vực đó có thể là chiến lược tốt nếu bạn có ít kinh nghiệm hơn trong các lĩnh vực khác.

Một chiến thuật khác là dựa vào số điểm mà bạn sẽ nhận được khi giải được thử thách đó. Thông thường, thử thách càng khó thì điểm nhận được càng cao. Một số người sẽ thích giải các thử thách khó trước để đặt được vị trí cao trên bảng xếp hạng và áp lực lên những người chơi khác. Một số khác thì sẽ làm từ câu dễ đến câu khó.

Chiến lược cá nhân của mình là kết hợp của tất cả. Mình sẽ giải từ các bài dễ trước sau đó đi lên dần và nhảy sang các lĩnh vực forensics khác nếu mình bị kẹt

Có một số người chơi khi biết rằng họ đã có flag, họ sẽ trì hoãn việc submit flag cho đến cuối cuộc thi để khiến cho bảng xếp hạng trở nên khó đoán hơn. Một vài cuộc thi CTF sẽ đóng băng bảng điểm trong 15-30 phút cuối để tạo tính bất ngờ cho cuộc thi. Mình khuyên các bạn không nên sử dụng chiến thuật này, nhưng nếu bạn đủ tự tin rằng flag của mình là đúng thì bạn có thể thử. Đừng bao giờ chủ quan khi thấy mình có một vị trí tốt trên bảng xếp hàng. Đó có thể chính là cái bẫy tâm lý mà những đội chơi “ỉm” flag giăng ra. Và chỉ trì hoãn việc submit flag khi bạn chắc chắn đó là flag đúng.

![pic](/assets/TungPost/Forensic_in_CTF/you_miss.webp)

## 0x7 Bài học rút ra

---

Hãy tự hỏi bản thân rằng “Mình sẽ nhận được gì từ việc chơi CTF?”. Đáp án của câu hỏi này còn tuỳ thuộc vào cách bạn cảm nhận khi chơi CTF. Đây là một số điều mình rút ra được từ những trải nghiệm của mình.

### Hãy tập viết mọi thứ

Một trong những điều mình thích nhất sau mỗi giải CTF đó là viết blog writeups về cuộc thi đó. Nếu có thử thách mà mình không hoàn thành được, mình sẽ đọc write-ups, làm lại, sau đó viết lại write-ups theo hướng hiểu của mình. Việc này không chỉ giúp mình lưu lại những kết quả của mình để có thể sử dụng trong tương lai mà còn giúp mình cải thiện kĩ năng viết các blogs mang tính kĩ thuật. 

Tài liệu đóng vai trò rất quan trọng trong quá trình điều tra, vậy nên ít nhất thì bạn cũng nên viết lại những ghi chú ngắn để sau trong trường hợp bạn muốn biết những artifacts (sẽ nói thêm về khái niệm này ở blog sau) này ở đâu thì có thể xem lại. Phần tuyệt vời nhất đó là không phải thử thách nào cũng có cách giải như nhau. Mình thực sự yêu thích việc được đọc những suy luận khác nhau của các thí sinh khác để có thể đạt đến kết quả cuối cùng.

![pic](/assets/TungPost/Forensic_in_CTF/documents.png)

### **Không ngừng thử thách bản thân và xây dựng sự tự tin**

Mình sẽ nhấn mạnh lại lần nữa, chơi CTF sẽ giúp bạn học hỏi. Đối với những người không thể làm việc với một số tệp bằng chứng khác nhau như tệp Linux hoặc pcap, CTF sẽ cung cấp cho bạn tình huống mô phỏng để bạn có thể học cách phân tích chúng. Trước khi chơi CTF, mình hiếm khi chạm trán với các tệp PCAP chứ chưa nói đến cách sử dụng Wireshark để lấy các tệp hoặc gói tin cụ thể ra. Tìm hiểu về hoạt động xuất của Google Takeout đã mang lại cho mình cái nhìn mới về những bằng chứng tiềm năng nào có thể được tìm thấy trên cloud và những gì có thể không được tìm thấy trực tiếp trên thiết bị di động. Đây chỉ là một vài ví dụ về việc thoát ra khỏi vùng an toàn của bạn và thử thách bản thân để tìm hiểu về các công cụ và kỹ thuật mới.

Và một điều quan trọng khác đó là hãy luôn giữ niềm tin mãnh liệt vào bản thân mình. Chỉ vì bạn chơi không tốt trong lần đầu chơi không có nghĩa là bạn không thể tiến bộ hơn. Mình biết khi mới bắt đầu, mình đã gặp khó khăn trong các cuộc thi. Mình không biết phải để tìm câu trả lời ở đâu hoặc làm cách nào để tìm được chúng. Tất cả đều quay trở lại với việc thực hành. Bất kỳ vận động viên nào cũng sẽ nói với bạn rằng việc lặp lại một nhiệm vụ sẽ chỉ khiến bạn giỏi hơn ở nhiệm vụ đó, và điều này cũng không khác gì với các cuộc thi CTF và các kỳ thi. Nếu bạn gặp một vấn đề đủ thường xuyên, bạn sẽ bắt đầu thấy các quy luật giống như Neo trong bộ phim the Matrix (Hay lắm đó, nên xem nhé).

![pic](/assets/TungPost/Forensic_in_CTF/iknowkungfu.png)

### **Hãy chơi thật vui vẻ!**

Điều quan trọng nhất khi tham gia các cuộc thi CTF là phải vui vẻ! Hãy coi nó như một bài tập rèn luyện để kích thích trí óc và giúp bạn thoát khỏi những công việc nhàm chán lặp đi lặp lại hàng ngày của bạn. Đừng căng thẳng, hãy tiếp tục học hỏi. Nếu bạn tham gia thi đấu trực tiếp, hãy tận hưởng tinh thần đồng đội với những thí sinh khác và mở rộng mối quan hệ của mình. Bạn sẽ không bao giờ biết được mình sẽ gặp ai và sẽ tạo dựng tình bạn với ai trong ngành này khi tham gia chơi CTF. 

Lời cuối, mình hy vọng rằng blog này sẽ mang lại cái nhìn mới và động lực cho bạn khi tham gia các cuộc thi CTF. Chúc may mắn và hẹn gặp lại tất cả các bạn trên “chiến trường” CTF!