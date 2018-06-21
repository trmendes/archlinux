# Allow for multi-line input:
/set weechat.bar.input.size 0
/set weechat.bar.input.size_max 3
# Hide join/leave messages in IRC (After enabling freenode)
/set irc.look.smart_filter on
/filter add irc_smart * irc_smart_filter *
# Making it look a bit nicer:
#/set weechat.look.prefix_same_nick "\u2937"
#/set weechat.look.prefix_error "\u26a0"
#/set weechat.look.prefix_action "\u26a1"
#/set weechat.look.bar_more_down "\u25bc\u25bc"
#/set weechat.look.bar_more_left "\u25c0\u25c0"
#/set weechat.look.bar_more_right "\u25b6\u25b6"
#/set weechat.look.bar_more_up "\u25b2\u25b2"
#/set weechat.look.prefix_suffix "\u2561"
/set weechat.color.chat_nick_colors red,green,brown,blue,magenta,cyan,white,lightred,lightgreen,yellow,lightblue,lightmagenta,lightcyan
/set weechat.color.separator 31
/set buffers.color.current_fg 31
/set buffers.color.current_bg white
/set buffers.color.hotlist_message_fg 229
/set buffers.color.hotlist_private_fg 121
/set buffers.color.hotlist_highlight_fg 163
/set buffers.color.number 239
/set buffers.color.number_char 245
/set weechat.bar.title.conditions "${inactive}"
/set weechat.bar.title.color_fg black
/set weechat.bar.title.color_bg 31         #Or white if 31 does nothing
# Away checks and limits
/set irc.server_default.away_check 10
/set irc.server_default.away_check_max_nicks 25
# Toggle nicklist with esc n
/key bind meta-n /bar toggle nicklist
# Create and customise activetitle bar
/bar add activetitle window top 1 0 buffer_title
/set weechat.bar.activetitle.priority 500
/set weechat.bar.activetitle.conditions "${active}"
/set weechat.bar.activetitle.color_fg white
/set weechat.bar.activetitle.color_bg 31
/set weechat.bar.activetitle.separator on
# Customize the title bar
/set weechat.bar.title.conditions "${inactive}"
/set weechat.bar.title.color_fg black
/set weechat.bar.title.color_bg 31
# Create and customise the rootstatus bar
/bar add rootstatus root bottom 1 0 [time],[buffer_count],[buffer_plugin],buffer_number+:+buffer_name+(buffer_modes)+{buffer_nicklist_count}+buffer_filter,[bitlbee_typing_notice],[lag],[aspell_dict],[aspell_suggest],[hotlist],completion,scroll
/set weechat.bar.rootstatus.color_fg 31
/set weechat.bar.rootstatus.color_bg 234
/set weechat.bar.rootstatus.separator on
/set weechat.bar.rootstatus.priority 500
/bar del status
/bar set rootstatus name status
# Create and customise the rootinput bar
/bar add rootinput root bottom 1 0 [buffer_name]+[input_prompt]+(away),[input_search],[input_paste],input_text,[spell_correction]
/set weechat.bar.rootinput.color_bg black
/set weechat.bar.rootinput.priority 1000
/bar del input
/bar set rootinput name input
# Customise the nicklist bar
/set weechat.bar.nicklist.color_fg 229
/set weechat.bar.nicklist.separator on
/set weechat.bar.nicklist.conditions "${nicklist} && ${window.number} == 1"
/set weechat.bar.nicklist.size_max 14
/set weechat.bar.nicklist.size 14
# Install iset.pl script manager:
/script install iset.pl go.py colorize_nicks.py autosort.py highmon.pl

# Highmon
/set plugins.var.perl.highmon.alignment "nchannel"
/window splith 15
/buffer highmon

# Go.py
/key bind meta-g /go

# Save
/set weechat.look.save_layout_on_exit all
/save

# Freenode
/server add freenode chat.freenode.net/6697 -ssl
/set weechat.network.gnutls_ca_file "/etc/ssl/certs/ca-certificates.crt"
