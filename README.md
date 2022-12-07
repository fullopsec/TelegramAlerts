Youtube video: https://youtu.be/-bmppdlnxEQ

This video show how to receive Instant Telegram message upon ssh login. You'll be notified of IP address and who logged in. You can also add more info if you want.

find @BotFather and type /start on Telegram
 
Create a new bot and follow instructions
  
You now have a bot and a API token(REMEMBER IT)!
  
Create a new Channel in Telegram and add your bot as a member

Give him rights to post messages

Message the channel

Use the API token/Bot token you just got to navigate to:

>https://api.telegram.org/bot<API_TOKEN>/getUpdates

Post a new message and quickly refresh the API request

Copy your channel/Group ID 

Send a message via the API:

>curl 'https://api.telegram.org/bot<API_TOKEN>/sendMessage?chat_id=<channel_id>&text=<text>'

It works but we can't use spaces
Let's improve this!

create a file and copy this code:
(Replace the correct values with yours)
>touch telegram.sh

```
#!/bin/bash
    
GROUP_ID=<group_id>
BOT_TOKEN=<bot_token>

# Detect if use ask for help
if [ "$1" == "-h" ]; then
  echo "Usage: `basename $0` \"text message\""
  exit 0
fi

if [ -z "$1" ]
  then
    echo "Add message text as second arguments"
    exit 0
fi

if [ "$#" -ne 1 ]; then
    echo "You can pass only one argument. For string with spaces put it on quotes"
    exit 0
fi
#send the message 
curl -s --data "text=$1" --data "chat_id=$GROUP_ID" 'https://api.telegram.org/bot'$BOT_TOKEN'/sendMessage' > /dev/null

```

Make the file executable
> chmod +x telegram.sh

Try it:
./telegram.sh "hello"

Let's put it with the binaries:
>sudo mv telegram.sh /usr/bin/telegram

Set it as owned by root
>sudo chown root:root /usr/bin/telegram

Secure it with permissions to prevent privilege escalation:
>sudo chmod 755 /usr/bin/telegram

Try the new command:
>telegram "how are you"

Lets make the ssh notifier!
>touch notify.sh

Write this inside notify.sh

```
#!/bin/bash
    
# prepare any message you want
login_ip="$(echo $SSH_CONNECTION | cut -d " " -f 1)"
login_date="$(date +"%e %b %Y, %a %r")"
login_name="$(whoami)"

# For new line I use $'\n' here
message="New login to "$'\n'"$login_name"$'\n'"$login_ip"$'\n'"$login_date"

#send it to telegram
telegram "$message"
```

move it to /etc/profile.d/notify.sh:
>sudo mv notify.sh /etc/profile.d/notify.sh

Disconnect and Reconnect to your machine. It should work
