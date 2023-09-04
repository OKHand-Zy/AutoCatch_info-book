# BIOS
BIOS（英文：Basic Input/Output System），即基本輸入輸出系統，亦稱為ROM BIOS、System BIOS、PC BIOS，是在通電啟動階段執行硬體初始化，以及為作業系統提供運行時服務的韌體。

### Server 開機時的運作：
簡單來說就是會在 Server 通電後啟動時，先載入 BIOS 並載入對底層的基礎設置與控制，之後進行加電自我檢測階段(POST)，在這階段會檢查您的所有硬體進行識別,系統測試,及初始化設定各硬體裝置等...，以確保其正常工作加載作業系統並提供運行時服務，之後才會依 Boot oder 設定順序才會進到我們的作業系統。我們可以在 POST階段 前進入 Bios 設定進行修改我們要對硬體或 Boot oder 順序上的設定。

![BIOS_Boot—流程](https://linux.vbird.org/linux_basic/centos7/0510osloader//osloader-flow-initramfs.jpg)
> BIOS 開機流程 (圖源：鳥哥部落格)

### 加電自檢
加電自檢又稱為啟動自我檢測（英語：Power-On Self-Test；POST），是計算機BIOS的一個功能，在啟動時會執行，針對計算機硬體如CPU、主機板、記憶體等進行檢測，結果會顯示在螢幕上。

### 基本輸入/輸出系統如何工作？
BIOS 是您啟動電腦時運行的第一個軟體程式。當您打開電腦時，BIOS 會首先測試硬體以確保一切正常。接下來，它初始化並識別系統的所有硬體。然後，它將作業系統加載到快取中並手動控制它。最後，BIOS 在電腦運行時為作業系統和應用程式提供各種可用的服務。這些服務包括電源管理、硬體配置和啟動管理。


## Reference：
[什麼是 BIOS ? 聽起來好難懂 ? 本篇文章用最白話的方式告訴您](https://tw.easeus.com/diskmanager/what-is-bios.html)

[加電自檢](https://zh.wikipedia.org/zh-tw/%E5%8A%A0%E7%94%B5%E8%87%AA%E6%A3%80)

[電腦怎麼開機的?](https://ithelp.ithome.com.tw/articles/10308633)

[開機流程、模組管理與 Loader  - 鳥哥](https://linux.vbird.org/linux_basic/centos7/0510osloader.php)
