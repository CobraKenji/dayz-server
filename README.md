# dayz-server
   
Ubuntu 18 specific issues apparently, these were my exact steps. Likely we can just add-apt-repo, update, upgrade and ignore the wget and fix-broken stuff...   
```
su - mysudouser

wget https://download.opensuse.org/repositories/Emulators:/Wine:/Debian/xUbuntu_18.04/amd64/libfaudio0_19.07-0~bionic_amd64.deb
wget https://download.opensuse.org/repositories/Emulators:/Wine:/Debian/xUbuntu_18.04/i386/libfaudio0_19.07-0~bionic_i386.deb
sudo dpkg -i libfaudio0_19.07-0~bionic_amd64.deb libfaudio0_19.07-0~bionic_i386.deb

sudo apt --fix-broken install
sudo apt update
sudo apt upgrade
sudo apt --fix-broken install
sudo apt autoremove --purge
sudo apt upgrade

sudo add-apt-repository ppa:cybermax-dexter/sdl2-backport
sudo apt update
sudo apt upgrade

# https://www.systutorials.com/maximum-number-of-mmaped-ranges-and-how-to-set-it-on-linux/
sysctl -w vm.max_map_count=655300
```   
   
Ensure the required ports are set.   
Game INBOUND 2302 udp   
Steam INBOUND 2304,27016 udp   
```
sudo iptables -A INPUT -p udp -m udp -m multiport --dports 2302,2304,27016 -j ACCEPT
sudo apt install iptables-persistent -y
```
   
Next we need to run this:   
```
wget -qN https://raw.githubusercontent.com/CobraKenji/dayz-server/main/script/dayz_setup.sh && bash dayz_setup.sh
```
to install...
0 winehq-stable
0 xvfb
0 tmux
0 nano
0 htop
0 curl
0 wget
0 psmisc
0 lib32gcc1
0 libstdc++6
0 libstdc++6:i386
    
Above script will create a new user named `dayz` and switch to that user.
As the new `dayz` user, we need to run:
```
wget -qN https://raw.githubusercontent.com/CobraKenji/dayz-server/main/script/dayzserver && bash dayzserver

# https://forums.dayz.com/topic/239635-dayz-server-files-documentation/
./dayzserver cfg

# Now you should be able to start the server (for the first start, follow script output)
./dayzserver start

# Press "Ctrl + b" then "d" to exit console.
# Do NOT press CTRL+c to exit! It will shut down the server
./dayzserver console
```
   

Logfiles (location)   
You can find the Logfiles under "serverfiles/logs" (folder will be created upon serverstart with -dologs parameters)   
   
The server_console.log content is from the Game Console To get the server_console.log, add this to the serverDZ.cfg:   
`logFile = "server_console.log";// Saves the server console log to a file in the folder with the other server logs`   
   
You also have to enable logs for the game through the start parameter (if not done yet)   

run ./dayzserver cfg type in 1 or script remove the # from the line with the logs to enable them   
   
Following command will link the console logs to the location above:   

```
ln -s ~/.wine64/drive_c/users/dayz/Local\ Settings/Application\ Data/DayZ/server_console.log ~/serverfiles/logs/server_console.log && ln -s ~/.wine64/drive_c/users/dayz/Local\ Settings/Application\ Data/DayZ/script.log ~/serverfiles/logs/script.log
```   
   
Modifying the Mission   
If you are up to modify the Mission, keep in mind that an update can overwrite the files!   
   
Make a copy of it and change whatever you want!   
   
`cp -r ~/serverfiles/mpmissions/dayzOffline.chernarusplus/ ~/serverfiles/mpmissions/mydayzmission.chernarusplus/`   
   
Don't forget to change the name in the serverDZ.cfg!!!   
   
`class Missions { class DayZ { template="mydayzmission.chernarusplus"; // Mission to load on server startup. . }; };`
   
Files that may be interesting for you (within the missionfolder):   

0 init.c
0 db/globals.xml
0 db/events.xml
0 db/economy.xml
0 db/types.xml

https://github.com/CobraKenji/dayz-server.git
