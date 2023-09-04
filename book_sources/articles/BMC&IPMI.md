# BMC & IPMI
### BMC ( Baseboard Management Controller )
BMC 是在 Server Mother board 上一顆可以獨立運作的 SoC (System on Chip)，可以把它視為一個獨立的嵌入式系統在 Mother board 上面方便我們利用 IPMI 對 Mother board 上的主系統去監控及操控的動作。

![BMC 架構](https://pic2.zhimg.com/80/v2-049ad9bd408399545018f6b655486359_1440w.webp)
> BMC 架構 (圖源：wiki BMC)

![BMC經典架構](https://pic2.zhimg.com/80/v2-cd4d230e9825febdfed66afd34aecd6d_1440w.webp)
> BMC 經典架構 (圖源：嵌入式操作系统那些事儿)

![BMC port 實體圖](https://www.ibm.com/support/pages/system/files/inline-images/BMC_ports_mapping_0.png)
> BMC port 實體圖 (圖源：IBM Support web)

---

### IPMI（Intelligent Platform Management Interface）
智慧型平台管理介面（Intelligent Platform Management Interface）能夠橫跨不同的作業系統、韌體和硬體平台，可以智慧型的監視、控制和自動回報大量伺服器的運作狀況，以降低伺服器系統成本。目前比較通用的版本為 **IPMI v1.5** 與 **IPMI v2.0**

**IPMI** 是獨立於作業系統外自行運作，並容許管理者即使在缺少作業系統或系統管理軟體、或受監控的系統關機但有接電源的情況下仍能遠端管理系統。
**IPMI** 也能在作業系統啟動後活動，與系統管理功能一併使用時還能提供加強功能，IPMI 只定義架構和介面格式成為標準，詳細實作可能會有所不同。

IPMI 1.5版和之後的版本能透過直接**串接、區域網路、或Serial over LAN**到遠端送出警報，系統管理員能使用 IPMI 訊息去查詢平台狀態、檢視硬體的日誌(log)、或透過相同連線從遠端控制台發出其他要求，這個標準也為系統定義了一個警報機制送出簡單網路管理協定(SNMP) 平台事件圈套(PET, Platform Event Trap)。

**IPMI 1.5：** 允許 IPMI 系统通過串口，BMC 專用的带 internet port，或者與 Mother board 共享的 Intranet port（NC-SI）與遠程管理系統通訊。

**IPMI 2.0：** 增加了SOL（serial over LAN）、群组管理、增强身份認證（RAKP+、SHA-1等）、基於 OpenSSL/RCMP+ 的安全增強網路接口、硬體方面的防火牆和 VLAN 支持等...。其中，SOL 支持將 BIOS 輸出和操作系統終端重新定向到與 BMC 相連的 serial port，通過 IPMI 與遠程系统管理軟體連接。並且 IPMI2.0 兼容系统通常還提供 KVM over IP(基於 IP 的遠程鍵盤滑鼠顯示器連接)、遠程桌面和頁面服務器等功能，雖然這些功能並不屬於 IPMI 協議的一部分。


## Reference：
[IPMI - WIKI](https://zh.wikipedia.org/zh-tw/IPMI)

[嵌入式操作系统那些事儿 - 知乎](https://zhuanlan.zhihu.com/p/566580261)

[How to configure the BMC on HMC 7063-CR2 - IBM](https://www.ibm.com/support/pages/how-configure-bmc-hmc-7063-cr2)

[BMC / IPMI - benjr.tw](https://benjr.tw/11240)

[Platform Management FRU Information Storage Definition v1.0 - Intel](https://www.intel.com/content/dam/www/public/us/en/documents/specification-updates/ipmi-platform-mgt-fru-info-storage-def-v1-0-rev-1-3-spec-update.pdf)

[Intelligent Platform Management Interface Specification v2.0 rev. 1.1 - Intel](https://www.intel.com/content/dam/www/public/us/en/documents/product-briefs/ipmi-second-gen-interface-spec-v2-rev1-1.pdf)
