## Receive Instant Telegram Notification Upon SSH Login

## Video tutorial

**Youtube Video: [Receive Instant Telegram Message Upon SSH Login](https://youtu.be/-bmppdlnxEQ)**

## Setup

In this video, we learn how to receive instant Telegram messages upon SSH login. You will be notified of the IP address and who logged in. You can also add any other information you want.

1. Find @BotFather and type /start on Telegram.
2. Create a new bot and follow the instructions.
3. You now have a bot and an API token (REMEMBER IT!).
4. Create a new Channel in Telegram and add your bot as a member.
5. Give the bot rights to post messages.
6. Message the channel.
7. Use the API token/Bot token you just got to navigate to: `https://api.telegram.org/bot<API_TOKEN>/getUpdates`
8. Post a new message and quickly refresh the API request. You should see the API request with you message.
9. Copy your channel/Group ID.
10. Send a message via the API:
    ```
    curl 'https://api.telegram.org/bot<API_TOKEN>/sendMessage?chat_id=<channel_id>&text=<text>'
    ```
11. It works but we can't use spaces. Let's improve this!
12. Create a file and copy this code: (Replace the correct values with yours) `touch telegram.sh`
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

    # Send the message 
    curl -s --data "text=$1" --data "chat_id=$GROUP_ID" 'https://api.telegram.org/bot'$BOT_TOKEN'/sendMessage' > /dev/null
    ```
13. Make the file executable: `chmod +x telegram.sh`
14. Try it: `./telegram.sh "hello"`
15. Let's put it with the binaries: `sudo mv telegram.sh /usr/bin/telegram`
16. Set it as owned by root: `sudo chown root:root /usr/bin/telegram`
17. Secure it with permissions to prevent privilege escalation: `sudo chmod 755 /usr/bin/telegram`
18. Try the new command: `telegram "how are you"`
19. Let's make the SSH notifier! `touch notify.sh`
20. Write this inside notify.sh:
    ```
    #!/bin/bash
    
    # Prepare any message you want
    login_ip="$(echo $SSH_CONNECTION | cut -d " " -f 1)"
    login_date="$(date +"%e %b %Y, %a %r")"
    login_name="$(whoami)"

    # For new line I use $'\n' here
    message="New login to "$'\n'"$login_name"$'\n'"$login_ip"$'\n'"$login_date"

    # Send it to Telegram
    telegram "$message"
    ```
21. Move it to /etc/profile.d/notify.sh: `sudo mv notify.sh /etc/profile.d/notify.sh`
22. Disconnect and reconnect to your machine. It should work.
