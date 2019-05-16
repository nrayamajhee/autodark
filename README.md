# autodark

A simple bash program that automatically change gnome themes on sunrise and sunset.

This bash program changes from Solarized Light to Dark during sunset. Make sure these commands are present in your distro!

- git
- curl
- jq
- gsettings
- dconf

```bash
git clone https://gitlab.com/nrayamajhee/autodark.git
cd autodark

./autodark

sudo cp autodark.timer /usr/lib/systemd/user/
sudo cp autodark.service /usr/lib/systemd/user

systemctl enable --user autodark.timer
systemctl start --user autodark.timer

```

You can copy it ~/.local/bin/
and add it to PATH by editing either `.profile` or `.bash_profile` or `.bash_rc`


```bash
export PATH="$HOME/.local/bin:$PATH"
```


To run you must configure:

__your zipcode__
```bash
sunset=`curl -X GET 'http://api.openweathermap.org/data/2.5/weather?zip=70148,us&APPID=86b00cb2e90213e0679873b97c9378fb' -H 'Content-Type: application/json' | jq '.sys.sunset'`
```

Currently it locates New Orleans using zip code 70148.

__your gome-terminal user id__:
```bash
dconf write /org/gnome/terminal/legacy/profiles:/:b1dcc9dd-5262-4d8d-a863-c897e6d979b9/background-color "'rgb(0,43,54)'"
```

__your desired theme and vs code settting location__
```bash
jq '."workbench.colorTheme" = "Solarized Light"' /home/nishan/.config/Code\ -\ OSS/User/settings.json > tmp.$$.json && mv -f tmp.$$.json /home/nishan/.config/Code\ -\ OSS/User/settings.json 
```

Also load and start the systemd timer. Here's how it schedules the command every day.
```bash
"schedule")
	decide
	echo Now: $now Sun rises at: $sunrise Sun sets at: $sunset
	if (($now < $sunrise)); then
			echo Trigger sunrise
			systemd-run --user --on-calendar=@$sunrise $0
	fi
	if (($now < $sunset)); then
			echo Trigger sunset
			systemd-run --user --on-calendar=@$sunset $0
	fi
	$0
;;
```