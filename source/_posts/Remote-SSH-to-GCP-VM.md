---
title: Remote -SSH to GCP-VM
banner_img: img/SSHtoGCPVM.png
index_img: /img/SSHtoGCPVM.png
comment: 'remark42'
tags: SSH
---

# Remote -SSH to GCP VM

<video controls style="width: 100%; height: auto;">
<source src="https://lookdev.codeart3d.xyz/blog_video/vscode/Remote-SSH-to-GCP-VM.mp4" type="video/mp4">
</video>

## Step 1 install Remote - SSH extenxions

<img src="/codeArt-TechBlog/img/ssh/step1.png" width="800" style="display: block; margin-left: 0;">

## Step 2 google GCP 確定你的VM

<img src="/codeArt-TechBlog/img/ssh/step2.png" width="800" style="display: block; margin-left: 0;">

## Step 3 connect your VS Code ，快速鍵 F1 或 View > Command Palette

<img src="/codeArt-TechBlog/img/ssh/step3.png" width="800" style="display: block; margin-left: 0;">


## Step 4 搜尋Remote - SSH > Remote - SSH: Open SSH Configuration File

<img src="/codeArt-TechBlog/img/ssh/step4.png" width="800" style="display: block; margin-left: 0;">

### Step 4.2打開 使用者配置檔案 /User/codeart/.ssh/config

<img src="/codeArt-TechBlog/img/ssh/step4-2.png" width="800" style="display: block; margin-left: 0;">

#### 對應的參數
```json
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
    Host remote-ssh-demo // VM的名稱
    HostName 35.234.24.25 // 外部IP位址
    User wu_peiflower01 // 你的使用者名稱
    IdentityFile // 新增一組 Key
```

<img src="/codeArt-TechBlog/img/ssh/step5.png" width="800" style="display: block; margin-left: 0;">

## Step 6 打開Terminal  (終端機) 或 按熱鍵


<img src="/codeArt-TechBlog/img/ssh/step6.png" width="800" style="display: block; margin-left: 0;">

## Step 7 在終端機 cd 到 /User/codeart/ 資料夾下面 建立資料夾 mykeys 產生私錀

<img src="/codeArt-TechBlog/img/ssh/step7.png" width="800" style="display: block; margin-left: 0;">

```json
cd /User/codeart //使用者下的使用者名稱
```

## Step 8 建立一個名叫mykeys新資料夾，放私錀的地方
```json
mkdir mykeys
```
## Step 9 cd 到你的mykeys 子目錄下

```json
cd mykeys
```
<img src="/codeArt-TechBlog/img/ssh/step9.png" width="800" style="display: block; margin-left: 0;">

## Step 10 打入以下命令，產生ssh 公錀
```json
ssh-keygen -t rsa -f remote-ssh-demo -C wu_peiflower01 -b 2048
```

<img src="/codeArt-TechBlog/img/ssh/step10.png" width="800" style="display: block; margin-left: 0;">


## Step 11 打入 ls 查詢是否產生 remote-ssh-demo 私錀與 remote-ssh-demo.pub公錀
```json
ls
```

<img src="/codeArt-TechBlog/img/ssh/step11.png" width="800" style="display: block; margin-left: 0;">

## Step 12 把 私錀 加入 到 IdentityFile 底下
```json
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
Host remote-ssh-demo
    HostName 35.234.24.25
    User wu_peiflower01
    IdentityFile /Users/codeart/mykeys/remote-ssh-demo // 加到這裡
```

<img src="/codeArt-TechBlog/img/ssh/step12.png" width="800" style="display: block; margin-left: 0;">

## Step 13 [打開remote-ssh-demo.pub](http://打開remote-ssh-demo.pub) 公錀 並復製

<img src="/codeArt-TechBlog/img/ssh/step13.png" width="800" style="display: block; margin-left: 0;">

## Step 14 回到GCP VM 點擊編輯 往下拉到 **安全殼層金鑰 新增項目 貼上公錀 按下儲存**

<img src="/codeArt-TechBlog/img/ssh/step14.png" width="800" style="display: block; margin-left: 0;">

## Step 15 回到 VS cdoe  設定VS code 讓 VM 連接到你的VS Code ，快速鍵 F1 或 View > Command Palette

### 搜尋Remote - SSH > **Remote - SSH: Connect to Host 並點擊**
<img src="/codeArt-TechBlog/img/ssh/step15.png" width="800" style="display: block; margin-left: 0;">

<img src="/codeArt-TechBlog/img/ssh/step15-2.png" width="800" style="display: block; margin-left: 0;">

## Step 16 連線中..…點擊Continue
<img src="/codeArt-TechBlog/img/ssh/step16.png" width="800" style="display: block; margin-left: 0;">

<img src="/codeArt-TechBlog/img/ssh/step16-2.png" width="800" style="display: block; margin-left: 0;">

## Step 17 測試新增資料夾看看 open folder ，點擊OK

<img src="/codeArt-TechBlog/img/ssh/step17.png" width="800" style="display: block; margin-left: 0;">

## Step 18 點擊 信任

<img src="/codeArt-TechBlog/img/ssh/step18.png" width="800" style="display: block; margin-left: 0;">

## Step 19 測試建立一個web 資料夾, 是否可兩端同步
<img src="/codeArt-TechBlog/img/ssh/step19.png" width="800" style="display: block; margin-left: 0;">








