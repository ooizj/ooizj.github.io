## 如何给EmuDeck设置代理
注：首先需要有代理服务器  

以文本文件的方式编辑下载的EmuDeck.desktop  
在“Exec=”后面新增如下代理设置
```
export http_proxy="http://代理服务ip:代理服务端口" && export https_proxy="http://代理服务ip:代理服务端口" && 
示例：export http_proxy="http://192.168.1.3:7890" && export https_proxy="http://192.168.1.3:7890" && 
```

最终的EmuDeck.desktop
```shell
#!/usr/bin/env xdg-open
[Desktop Entry]
Name=Install EmuDeck
Exec=export http_proxy="http://代理服务ip:代理服务端口" && export https_proxy="http://代理服务ip:代理服务端口" && echo "Downloading EmuDeck AppImage, please wait" && mkdir -p ~/Applications && curl -L "$(curl -s https://api.github.com/repos/EmuDeck/emudeck-electron/releases/latest | grep -E 'browser_download_url.*AppImage' | cut -d '"' -f 4)" > ~/Applications/EmuDeck.AppImage && chmod +x ~/Applications/EmuDeck.AppImage && ~/Applications/EmuDeck.AppImage && exit
Icon=steamdeck-gaming-return
Terminal=true
Type=Application
StartupNotify=false

```
