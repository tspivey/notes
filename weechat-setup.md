# WeeChat Setup for screen reader users
This guide is meant to take screen reader users completely new to [WeeChat](https://www.weechat.org) from a fresh installation to a fully customized setup,
and layout enough usage instructions to be able to successfully use WeeChat as an IRC client without too many accessibility problems.
This guide will not walk you through installing WeeChat, nor will it teach you basic IRC usage. There are already great resources on the net for that.
WeeChat is packed with tons of options, and has excellent [documentation](https://weechat.org/doc/).

## WeeChat Options

Don't notify of activity for everything, just highlights, private messages, etc:

    /set weechat.look.buffer_notify_default highlight

By default, pressing `alt-0`-`9` to jump to a buffer, then the same hotkey again jumps back to the previous buffer, to quickly read a buffer then go back.
This is great if you can see, but confusing and useless for screen reader users who will need to move their fingers away from the hotkey combination to read the newly active buffer.
Disable it so that pressing `alt-0`-`9` when you're already in that buffer does nothing:

    /set weechat.look.jump_current_to_previous_buffer off

Disable prefix alignment, so that everything goes to the left:

    /set weechat.look.prefix_align none

Hide the buff list (use `/buffer list` to list buffers in the status window)

    /bar hide buflist on

Hide the nicklist (use `/names #channel` to print the nicks):

    /bar hide nicklist

Remove the current time from the status bar:

    /set weechat.bar.status.items [buffer_last_number],[buffer_plugin],buffer_number+:+buffer_name+(buffer_modes)+{buffer_nicklist_count}+buffer_zoom+buffer_filter,scroll,[lag],[hotlist],completion

Show < and > around nicks:

    /set weechat.look.nick_prefix <
    /set weechat.look.nick_suffix >

Don't show message counts in the hotlist:

    /set weechat.look.hotlist_count_max 0

Add smart filter for joins/parts/quits:

    /filter add irc_smart * irc_smart_filter *

*Note*: pressing `alt-k` then a hotkey will put the name required for the keybinding, along with an assigned binding on the input line.
For example: pressing `alt-k` then `control-p` will put "ctrl-P /buffer -1" on the input line.
This is a nice way to see if a key has already been bound, and since it adds nothing if the key isn't bound,
can also be used when setting bindings.

Bind `alt+c` to close the current buffer:

    /key bind meta-c /close

or instead of manually typing `meta-c`, press `alt-k alt-c`.

Toggle timestamps (always sets the format to `%H:%M:%S`:

    /trigger add cmd_toggle_time command toggle_time
    /trigger set cmd_toggle_time command /mute /set weechat.look.buffer_time_format "${if:${WeeChat.look.buffer_time_format}==?${tg_argv1}:}"
    /key bind meta-t /toggle_time %H:%M:%S

Add sound triggers for highlight and private messages;
assumes a directory in `~/.WeeChat/sounds` contains `highlight.wav` and `private.wav`, and that the `play` command exists on your machine:

    /trigger addoff snd_highlight print
    /trigger set snd_highlight conditions ${tg_highlight} == 1 && ${tg_displayed} == 1 && ${buffer.notify} > 0 && ${tg_tags} ~= ,notify_message,
    /trigger set snd_highlight command /mute exec -bg play ~/.WeeChat/sounds/highlight.wav
    /trigger enable snd_highlight

    /trigger addoff snd_private print
    /trigger set snd_private conditions ${tg_msg_pv} == 1 && ${tg_displayed} == 1 && ${buffer.notify} > 0 && ${tg_tags} ~= ,notify_message,
    /trigger set snd_private command /mute exec -bg play ~/.WeeChat/sounds/private.wav
    /trigger enable snd_private

For the above, you might want to also disable beeping on highlight and private messages, if you are sure these sounds ar working:

    /trigger disable beep

It is a bad idea to store your server passwords in the clear. If you don't mind being prompted for a password when you start WeeChat,
Use the secure store (in sec.conf) to encrypt your passwords.
They will be decrypted in memory using the password you entered when starting WeeChat:

    /secure passphrase your_super_secret_passphrase

## IRC Options
Set your default nicks, real name, and username;
set realname and username to something bogus if you don't want those published to servers:

    /set irc.server_default.nicks your_nick,your_nick2
    /set irc.server_default.realname Your Name
    /set irc.server_default.username yourusername

Add a server with no ssl and no password:

    /server add server1 irc.server1.example.com/6667

Add a server with ssl, a username, a password, which autoconnects on startup:
First store the server's password in secure storage (WeeChat will mask the password as you type):

    /secure set server2pw secret_pass
Add the server (password will be masked as you type):

    /server add server2 irc.server2.example.com/6697 -ssl -autoconnect -username=yourusername -password=${sec.data.server2pw}

Since you specified `${sec.data.server2pw}` as the password, the password is pulled from secure storage. Not only is this more secure,
if you use the same password for many servers (like multiple znc sessions), you only need to change it in one place.

To verify signed SSL certificates, for server certs signed by a CA, tell WeeChat about the ca file (from the WeeChat FAQ):

    /set weechat.network.gnutls_ca_file "/etc/ssl/certs/ca-certificates.crt"

*Note*: if you are running OS X with homebrew openssl installed, you can do:

    /set weechat.network.gnutls_ca_file "/usr/local/etc/openssl/cert.pem"

*Note*: Check that you have this file on your system (commonly brought by package "ca-certificates"). 

If your server has a self signed certificate, you'll need to get the fingerprint.
*Note*: Some IRC networks will give you a random server with a different fingerprint (irchighway), which can be really complicated to manage.
In the terminal, fetch the certificate from the server:

    openssl s_client -showcerts -servername irc.server2.example.com -connect irc.server2.example.com:6697 < /dev/null | sed -n '/-----BEGIN CERTIFICATE-----/,/-----END CERTIFICATE-----/p' > server2cert.pem

Then get the fingerprint:

    cat server2cert.pem | openssl x509 -sha512 -fingerprint -noout | tr -d ':' | tr 'A-Z' 'a-z' | cut -d = -f 2 > server2fingerprint.txt

The fingerprint will be in `server2fingerprint.txt`.
Set the ssl fingerprint (replace `<fprint>` with the fingerprint from `server2fingerprint.txt`):

    /set irc.server.server2.ssl_fingerprint <fprint>

Separate multiple fingerprints with commas.

Server2 will autoconnect when WeeChat starts since you specified -autoconnect, but server1 will not.
To connect to a preconfigured server manually:

    /connect server1

or disconnect:

    /disconnect server1

If you want to enable autoconnect for server1:

    /set irc.server.server1.autoconnect on

To adjust the notify level for one server/buffer (the name can be less specific):
    /set weechat.notify.irc.server1.#channel message

## Helpful scripts
Scripts can be installed with `/script install name`.
* go.py - adds a `/go` command to move to a buffer. Example: `/go #channel`. Partial names are also supported; `/go chan` will also work.


## Usage
When starting WeeChat, you will be prompted to enter your secure passphrase to decrypt your secure passwords if you set that option up.
WeeChat will immediately connect to servers with autoconnect enabled. Type `/connect [servername]` to connect to a specific server, or just `/connect` to connect to all servers.
Type `/disconnect [servername]` to disconnect from a server, or `/disconnect` to disconnect from all servers, and cancel autoreconnect.

Type `/set config.setting.name` to see its current value. * works here as a wildcard character.
`/set diff` shows which options were changed from their defaults.

If you didn't add the `alt-c` binding to close the current buffer, use `/close` to do the same thing.

Tab completion works just about everywhere.

## Useful hotkeys
* Go to previous buffer: `control-p`, `alt-left arrow`, `alt-up arrow`
* Go to next buffer: `control-n`, `alt-right arrow`, `alt-down arrow`
* Jump to buffers 1-10: `alt-0`-`9` (`alt-0` goes to buffer 10)
* Jump to buffer 00-99: `alt-j` `00`-`99`
* Go to last buffer with activity in hotlist: `alt-a`
* Clear hotlist: `alt-h`
* In the server buffer (`alt-1`), switch between connected servers: `control-x`
* Scroll up 1 screen: `PgUp`
* Scroll down 1 screen: `PgDn`
* Go to beginning of buffer: `alt-home`
* Go to end of buffer: `alt-end`
* Toggle filters: `alt-=`
