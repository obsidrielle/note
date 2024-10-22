# 1. OBS 无法录屏
使用的是 `Hyprland` 环境，改为使用 `xdg-desktop-portal-wlr` 和 `wlrobs`。
```
yay -S wlrobs xdg-desktop-portal-wlr
```
然后使用 `scpy` 源就可以录屏了。

# 2. FontConfig
```xml
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'urn:fontconfig:fonts.dtd'>
<fontconfig>
 <!-- Default system-ui fonts -->
 <match target="pattern">
  <test name="family">
   <string>system-ui</string>
  </test>
  <edit binding="strong" mode="prepend" name="family">
   <string>sans-serif</string>
  </edit>
 </match>
 <!-- Default sans-serif fonts-->
 <match target="pattern">
  <test name="family">
   <string>sans-serif</string>
  </test>
  <edit binding="strong" mode="prepend" name="family">
   <string>.PingFang SC</string>
   <string>Noto Sans</string>
   <string>Twemoji</string>
  </edit>
  <edit name="style">
	  <string>Semibold</string>
  </edit>
 </match>
 <!-- Default serif fonts-->
 <match target="pattern">
  <test name="family">
   <string>serif</string>
  </test>
  <edit binding="strong" mode="prepend" name="family">
   <string>Source Han Serif CN SemiBold</string>
   <string>Noto Serif</string>
   <string>Twemoji</string>
  </edit>
 </match>
 <!-- Default monospace fonts-->
 <match target="pattern">
  <test name="family">
   <string>monospace</string>
  </test>
  <edit binding="strong" mode="prepend" name="family">
   <string>FiraCode Nerd Font</string>
   <string>Symbols Nerd Font</string>
   <string>Twemoji</string>
  </edit>
 </match>
 <dir>~/.fonts</dir>
 <match target="font">
  <edit mode="assign" name="rgba">
   <const>rgb</const>
  </edit>
 </match>
 <match target="font">
  <edit mode="assign" name="hinting">
   <bool>true</bool>
  </edit>
 </match>
 <match target="font">
  <edit mode="assign" name="hintstyle">
   <const>hintfull</const>
  </edit>
 </match>
 <match target="font">
  <edit mode="assign" name="antialias">
   <bool>true</bool>
  </edit>
 </match>
</fontconfig>

```
