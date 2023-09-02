# How to capture data and analyze
## Dev ENV
 - Python 3.6 or later
 - python lirary：
   - telnetlib
   - paramiko
   - (Option)linux：pexpect ,Windows：wexpect or Linux&Windows：subprocess

## Dev
1. SSH :
    ```python
    import paramiko
    ssh_cnt = paramiko.SSHClient()
    ssh_cnt.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh_cnt.connect( hostname = <ssh_ip> ,port = <default 22> ,username = <ssh_loging_username> ,password = <ssh_loging_password> )
    ....   do some thing   ....
    ssh_cnt.close()
    ```
2. SOL (Serial over LAN) :
    ```python
    cmd = 'ipmitool -I lanplus -H {0} -U {1} -P {2} sol activate'.format(bmc_ip ,bmc_ipmi_username ,bmc_ipmi_password)
    sol_connect = pexpect.spawn(cmd) # or Use subprocess.Popen to run this command
    ....   do some thing   ....
    cmd = 'ipmitool -I lanplus -H {0} -U {1} -P {2} sol deactivate'.format(bmc_ip ,bmc_ipmi_username ,bmc_ipmi_password)
    end_sol_connect = os.system(cmd) # or Use subprocess.Popen to run this command
    ```
3. Telnet :
    ```python
    telnet_connect = telnetlib.Telnet(host= <telnet_host_ip> ,port= <telnet_port> ,timeout=5)
    ....   do some thing   ....
    telnet_connect.close()
    ```

## suggestion
1. 每一個 server 或 bios 可能是設計上導致 decode bytes to str ,用的 decode 編碼都不相同。
2. 可以透過截取出來的 raw data 去分析一下這是要用哪一種編碼 decode (目前只遇過 utf-8 與 iso-8859-1)。
3. 簡單來說 因為 **iso-8859-1** 屬於單字節 每個 8 bites , 而 **utf-8** 屬於多字節 每個 1-4 bytes 導致兩者不能兼容。(Reference 有解釋文章)
4. 基本上在 bios 中截取出來的是 帶有 ANSI序列串 還有 顏色,光標等... 格式的字串 (像下面這樣)。
    ```bytes
    b'\x1b[2;1H\x1b[0;37;44m\x1b[2;1H    Main \x1b[0;34;47m Advanced \x1b[4;1H\x1b[4;1H\xb3\x10 Trusted Computing                                \x1e\xb3Option ROM Dispatch      \xb3'
    ```
    ![bios-imag](https://imgur.com/1PiqcZE.png)
    > 擷取圖範例
5. 可透過下列程式碼濾掉你想要的 ANSI序列串開頭格式 顏色 光標
    ```python
    content = raw_data
    content = content.decode('latin-1')
    # replace escape sequences with newlines
    content = re.sub(r'\x1b', '\n', content)
    # 刪除 彩色控制字元
    #content = re.sub(r'\[[0-9]m', '', content)
    #content = re.sub(r'\[[0-9][0-9]m', '', content)
    #content = re.sub(r'\[[0-9];[0-9];[0-9][0-9]m', '', content)
    #content = re.sub(r'\[[0-9];[0-9][0-9];[0-9][0-9]m', '', content)
    # 刪除 光標定位控制序列
    content = re.sub(r'\[[0-9];[0-9]H', '', content)
    content = re.sub(r'\[[0-9][0-9];[0-9]H', '', content)
    content = re.sub(r'\[[0-9];[0-9][0-9]H', '', content)
    content = re.sub(r'\[[0-9][0-9];[0-9][0-9]H', '', content)
    # 刪除 綠色光標
    content = re.sub(r'\x10', '', content)
    return content
    ```
6. 建議使用 regex 尋找你要的資料並擷取出來 , 所以我們在過濾的過程中留下 bios 顏色當特徵讓你好去尋找目前的選項位置與目標。
7. 不管你現在用哪個 connect 方式 , 你擷取出來的 raw data 都能用同樣的編碼 decode。 \
    例如我現在用 SOL 擷取出 raw data 是用 utf-8 編碼 decode , 你用 Telnet 去擷取出的 raw data 一樣用 utf-8 編碼 decode 出來。

## Exemple to catch info
以選擇 Bios Page 為例
### SOL :
```python
page_name = 'you_went_find_page_name'
# connect SOL
cmd = 'ipmitool -I lanplus -H {0} -U {1} -P {2} sol activate'.format(config.bmc_external_ip, config.bmc_ipmi_username, config.bmc_ipmi_password)
sol_portcol = pexpect.spawn(cmd)
# 利用 bios 特性會一直循環所以我們一直往右刷新即可
sol_portcol.send(inputs_dic.get('RIGHT'))
while 1==1:
    sol_portcol.expect(['Any string', pexpect.EOF, pexpect.TIMEOUT], timeout=1)
    bios_log = bios_sol_Convert(sol_portcol.before) # bios_sol_Convert 是我過濾 ansi 開頭,光標等... function 可以參考上面第5點
    pattern = rf'\[0;34;47m\s{re.escape(page_name)}.*$' # 利用 Bios 特性目前 Page 會是灰底 所以利用 灰底色標+page_name 去尋找
    matches = re.findall(pattern, bios_log, flags=re.MULTILINE)
    if matches: # 如果有抓到就退出
        cmd = 'ipmitool -I lanplus -H {0} -U {1} -P {2} sol activate'.format(bmc_external_ip, bmc_ipmi_username, bmc_ipmi_password)
        pexpect.spawn(cmd)
        break
    else: # 沒抓到繼續往右
        sol_portcol.send(inputs_dic.get('RIGHT'))
        time.sleep(1)
```
### Telnet :
```python
# connect Telnet
telnet_portcol = telnetlib.Telnet(host=telnet_host_ip, port=telnet_port,timeout=5)
sendline_telnet_message(telnet_portcol,b'\033[C') # telnet 傳的東西需要轉成 bytes
while 1==1:
    bios_log = telnet_portcol.read_very_eager() # 擷取出目前頁面的 raw data
    pattern = rf'\[0;34;47m\s{re.escape(page_name)}.*$' # 利用 Bios 特性目前 Page 會是灰底 所以利用 灰底色標+page_name 去尋找
    matches = re.findall(pattern, bios_sol_Convert(bios_log), flags=re.MULTILINE)
    if matches: # 如果有抓到就退出
        telnet_portcol.close()
        time.sleep(1.5)
        return bios_sol_Convert(bios_log)
    else: # 沒抓到繼續往右
        sendline_telnet_message(telnet_portcol,b'\033[C')
        time.sleep(1)
```

