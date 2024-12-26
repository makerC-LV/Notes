# Linux Mint Desktop XFCE


#### Add an AppImage application to the dock
Solution: https://forums.linuxmint.com/viewtopic.php?t=300594

- right click on the menu botton and select edit applications. 
- select the heading you want your app to appear under hit the + sign
- fill in the info
- change the name from New Launcher to your desired name for the app.
- click on the icon button select an Icon you want to use (you may have to down load one for your app). 
- save (save button in the toolbar, unintuitive) and exit,
- you may have to log out and back in for it to show up. 

---
#### Add a general application to the dock

---
#### Problem with suspend
Suspending after inactivity was failing because a popup window was coming up asking for authentication. \
Solution: https://askubuntu.com/questions/543921/authentication-required-before-suspend

>`locate -b org.freedesktop.login1.policy`

The file is in 
>`/usr/share/polkit-1/actions/org.freedesktop.login1.policy`

In this file, below the entry for 
> `<action id="org.freedesktop.login1.suspend">`

set the entries to  
```
<defaults>
    <allow_any>yes</allow_any>
    <allow_inactive>yes</allow_inactive>
    <allow_active>yes</allow_active>
</defaults>
```



