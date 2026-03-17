# What is a socket?
# சாக்கெட் என்பது என்ன?

"சாக்கெட்" எனும் சொல்லை பல இடங்களில் நீங்கள் பயன்படுத்த கேட்டிருக்கலாம் ஆனால்
அது என்ன என்பது உண்மையில் குறித்து நீங்கள் யோசித்திருக்கலாம்.நல்லது
சுருக்கமாக சொல்லவேண்டுமென்றால் 

```
'சாக்கெட்டுகள்' என்பது நிரல்கள் பிறநிரல்களுடன் யூனிக்ஸ் 
கோப்பு அடையாளங்காட்டி (file descriptor) மூலம் பிற நிரல்களுடன் தொடர்புகொள்ளும் ஒரு வழி ஆகும்.
```

You hear talk of "sockets" all the time, and perhaps you are wondering
just what they are exactly. Well, they're this: a way to speak to other
programs using standard Unix [i[File descriptor]] file descriptors.

What?
என்ன?

சரி---சில யூனிக்ஸ் வல்லுநர்கள் கூறுவதை நீங்கள் கேட்டிருக்கலாம், "கடவுளே, யூனிக்ஸ்-ல் _எல்லமே_ ஒரு கோப்புதான்!""
அந்த வல்லுநர்கள் குறிப்பிடுவது எல்லாம் யூனிக்ஸ் நிரல்கள் எந்த வகையான உள்ளீடு/வெளியீடு (I/O) செயல்களை செய்யும் போதும் அது ஒரு கோப்பு அடையாளங்காட்டியினை படிக்கிறது அல்லது அதில் எழுதுகிறது. ஒரு கோப்பு அடையாளங்காட்டி எனப்படுவது யாதெனில் திறந்துள்ள கோப்பினை அடையாளம் காட்டும் ஒர் முழுஎண்(integer) ஆகும்.

ஒரு கோப்பிற்கும் நெட்வொர்கிற்கும் என்ன சம்மந்தம் என்று ஒரு கேள்வி வருகிறதா?
அதற்கான புரிதல்கொள்ள இந்த கோப்பு அடையாளங்காட்டியை நெட்வொர்க் மூலமாகவும் அனுக முடியும் என்று புரிந்து கொள்ளுங்கள். மேலும் அந்த கோப்பு ஒரு பிஃப்போ(FIFO), ஒரு பைப்(pipe),ஒரு முனையம் (terminal) (அ) ஒரு வட்டில் இருக்ககூடிய ஒரு கோப்பு என எந்த வகையிலும் இருக்கலாம். அகையால் நீங்கள் இணையத்தில் பிற நிரல்களுடன் தொடர்புகொள்ள ஒரு கோப்பு அடையாளங்காட்டியை பயன்படுத்தலாம்.  

Ok---you may have heard some Unix hacker state, "Jeez, _everything_ in
Unix is a file!" What that person may have been talking about is the
fact that when Unix programs do any sort of I/O, they do it by reading
or writing to a file descriptor. A file descriptor is simply an integer
associated with an open file. But (and here's the catch), that file can
be a network connection, a FIFO, a pipe, a terminal, a real on-the-disk
file, or just about anything else.  Everything in Unix _is_ a file! So
when you want to communicate with another program over the Internet
you're gonna do it through a file descriptor, you'd better believe it.

சரி, திரு. அறிவுக்கொழுந்தே நான்  இந்த நெட்வொர்க் மூலம் தொடர்புகொள்ளும் கோப்பு அடையாளங்காட்டியை பற்றி எப்படி அறிவது என உங்களின் மனம் கூறுவதை நான் அறிகிறேன்.
ஆகவே அதற்கான பதிலை பார்க்க தொடங்குவோம்.
நீங்கள்  [i[`socket()` function]] `socket()` அமைப்பு செயற்கூறினை அழைப்பீர்கள். அந்த செயற்கூறானது ஒரு சாக்கெட் அடையாளங்காட்டியை தரும் நீங்கள் அந்த சாக்கெட் அடையாளங்காட்டியை [i[`send()` function]] `send()` செயற்கூறினை பயன்படுத்தி அதற்கு உள்ளீடு அளிப்பீர்கள் மற்றும் [i[`recv()` function]] `recv()` செயற்கூறினை பயன்படுத்தி  வெளியீட்டினை படிப்பீர்கள்.
மேலும் விவரங்களுக்கு : ([`man send`](#sendman), [`man recv`](#recvman)) சாக்கெட் செயற்கூறுகளை படித்து பாருங்கள்.

"Where do I get this file descriptor for network communication, Mr.
Smarty-Pants?" is probably the last question on your mind right now, but
I'm going to answer it anyway: You make a call to the [i[`socket()`
function]] `socket()` system routine. It returns the [i[Socket
descriptor]] socket descriptor, and you communicate through it using the
specialized [i[`send()` function]] `send()` and [i[`recv()` function]]
`recv()` ([`man send`](#sendman), [`man recv`](#recvman)) socket calls.

"ஆஹ்!" நீங்கள் ஆச்சரியம் கொள்வது எனக்கு புரிகிறது. ஒரு கோப்பு அடையாளங்காட்டி எனில் நான் சாதாரணமாக [i[`read()` function]] `read()` and [i[`write()` function]] `write()` எனும் செயற்கூறுகள் வழியே பயன்படுத்த இயலுமே ஆனால் நான் ஏன்
[i[`send()` function]] `send()` மற்றும் [i[`recv()` function]] `recv()` செயற்கூறுகளை பயன்படுத்தும் போது தரவு அனுப்பி பெறுதலில் நிறைய கட்டுப்பாட்டினை அளிக்கிறது.

"But, hey!" you might be exclaiming right about now. "If it's a file
descriptor, why in the name of Neptune can't I just use the normal
[i[`read()` function]] `read()` and [i[`write()` function]] `write()`
calls to communicate through the socket?"  The short answer is, "You
can!"  The longer answer is, "You can, but [i[`send()` function]]
`send()` and [i[`recv()` function]] `recv()` offer much greater control
over your data transmission."

சரி பின்னர் என்ன இருக்கிறது? பல வகையான சாக்கெட்டுகள் இருக்கின்றன, இணைய சாக்கெட்டுகள் DARPA Internet addresses (Internet Sockets), 
கணினியில் இருக்கும் கோப்பு பெயர்கள் (Unix Sockets),
CCITT X.25 addresses (X.25 சாக்கெட்டுகளை நீங்கள் தவிற்துவிடலாம்.)
மேலும் யூனிக்ஸின் எந்த அமைப்பை பயன்டுத்துகிறீர்கள் என்பதை பொருத்து இன்னும் சாக்கெட் வகைகளின் பட்டியல் சற்று நீளமானதாக கூட இருக்கலாம்.
இந்த ஆவணமானது முதல் வகை இணைய சாக்கெட்டுகளை பற்றி மட்டுமே பேசப்போகிறது.

What next? How about this: there are all kinds of sockets. There are
DARPA Internet addresses (Internet Sockets), path names on a local node
(Unix Sockets), CCITT X.25 addresses (X.25 Sockets that you can safely
ignore), and probably many others depending on which Unix flavor you
run.  This document deals only with the first: Internet Sockets.


## Two Types of Internet Sockets
## இரு வகையான இணைய சாக்கெட்டுகள்

என்ன இது? மீண்டும் இரு வகைகளா? ஆம்.
சற்றே சரியாக கூறவேண்டுமென்றால் நான் பொய்தான் கூறுகிறேன்.
இன்னும் நிறைய வகைகள் இருக்கின்றன நான்  இணைய சாக்கெட்டுகளின் இரு வகைகளைப்பற்றி மட்டுமே குறிப்பிட போகிறேன். பின்வரும் ஒரு சொற்றொடரில் மட்டும் மேற்கூறியதை தவிர்த்து விடுங்கள். நான் [i[Raw sockets]] "Raw Sockets" என்று ஒன்றும் இருக்கிறது அதைப்பற்றி நீங்களே அறித்துகொள்ளுங்கள்.

What's this? There are two types of Internet sockets? Yes. Well, no. I'm
lying. There are more, but I didn't want to scare you.  I'm only going
to talk about two types here. Except for this sentence, where I'm going
to tell you that [i[Raw sockets]] "Raw Sockets" are also very powerful
and you should look them up.

சரி மீண்டும் தலைப்பிற்கே வருவோம் அந்த இருவகை இனைய சாக்கெட்டுளில் 
ஒன்று [i[Stream sockets]]  "Stream சாக்கெட்டுகள்"
மற்றொன்று [i[Datagram sockets]] "Datagram சாக்கெட்டுகள்"
பின்வரும் பகுதிகளில் அவற்றை முறையே [i[`SOCK_STREAM` macro]] "`SOCK_STREAM`"
[i[`SOCK_DGRAM` macro]] "`SOCK_DGRAM`" என பயன்படுத்துவோம். 
"Datagram சாக்கெட்டுகள்" [i[`connect()` function]] `connect()`' செயல்கூறினை கொண்டிருந்தாலும்  
சில சமயங்களில் இணைப்பில்லா சாக்கெட்டுகள் என்று அழைக்கப்படுவதுண்டு.
மேலும் அறிய கீழேயுள்ள [`connect()`](#connect) பகுதியை படியுங்கள்.

All right, already. What are the two types? One is [i[Stream sockets]]
"Stream Sockets"; the other is [i[Datagram sockets]] "Datagram Sockets",
which may hereafter be referred to as [i[`SOCK_STREAM` macro]]
"`SOCK_STREAM`" and [i[`SOCK_DGRAM` macro]] "`SOCK_DGRAM`",
respectively. Datagram sockets are sometimes called "connectionless
sockets".  (Though they can be [i[`connect()` function]] `connect()`'d
if you really want. See [`connect()`](#connect), below.)

Stream சாக்கெட் மிகவும் நம்பகமான இருவழி தொடர்பு இணைப்பு.
நீங்கள் இரு தரவுகளை இதில் அனுப்பினால் உதாரணமாக 1 , 2 என்பதை அனுப்புகிறீர்கள்
எனக் கொள்வோம். தகவல் பெறும்முனையிலும் தகவல் அதே வரிசையில் 1,2 என்றே கிடைக்கும்.
மேலும் அவை பிழைகள் இல்லாமல் இருக்கும். இதன் நம்பகத்தன்மை எந்த அளவிற்கு இருக்குமெனில்
யாரேனும் இதற்கு மறுப்பு தெரிவித்து மல்லுக்கு வந்தால் நான் கவலையில்லாமல் காதுகளை பொத்திக்கொண்டு
லா லா லா லா என எதேனும் ஒன்றினை உளறிக்கொண்டிருக்கலாம்.

Stream sockets are reliable two-way connected communication streams. If
you output two items into the socket in the order "1, 2", they will
arrive in the order "1, 2" at the opposite end. They will also be
error-free. I'm so certain, in fact, they will be error-free, that I'm
just going to put my fingers in my ears and chant _la la la la_ if
anyone tries to claim otherwise.

ஒடை சாக்கெட்டுகளின் பயன்தான் என்ன? நீங்கள் [i[telnet]] `telnet` அல்லது [i[ssh]] `ssh`
போன்ற பயன்பாடுகளை பற்றி கேள்விபட்டிருக்கலாம் அவை stream சாக்கெட்டுகளையே பயன்படுத்துகின்றன.
நமக்கு அனுப்பியவை அனுப்பிய வரிசையிலே மறுமுனைக்கு சென்று சேரவேண்டுமல்லவா ?
மேலும் வலை உலவிகள்  மீஉரை நெறிமுறையை [i[HTTP protocol]] (HTTP) பின்பற்றுகிறது.
மீஉரை நெறிமுறையை வலைப்பக்கங்களை பெறுவதற்கு  stream சாக்கெட்டுகளையே பயன்படுத்துகிறது.
உதாராமாக நீங்கள் telnet பயன்படுத்தி பின்வரும் கட்டளையை உள்ளிட்டு இருமுறை RETURN விசையை தட்டினால்
அந்த வலைப்பக்கத்தின் உள்ளடக்கம் தங்களுக்கு வெளியீடாக கிடைக்கும்.

What uses stream sockets? Well, you m have heard of the [i[telnet]]
`telnet` or `ssh` applications, yes? They use stream sockets. All the
characters you type need to arrive in the same order you type them,
right? Also, web browsers use the Hypertext Transfer Protocol [i[HTTP
protocol]] (HTTP) which uses stream sockets to get pages. Indeed, if you
telnet to a web site on port 80, and type "`GET / HTTP/1.0`" and hit
RETURN twice, it'll dump the HTML back at you!

> உங்களிடம் `telnet` நிறுவிய கணினி இல்லையா ? நீங்கள் நிறுவ விரும்பவில்லையா
> (அ) உங்களின் `telnet` ஆனது குறிப்பிட்ட வேலைகளுக்கு மட்டும் வேலை செய்கிறதா?
> கவலையை விடுங்கள் இந்த கையேட்டுடன் `telnet` போன்றதொரு பயன்பாட்டினை
> [flx[`telnot`|telnot.c]] அளிக்கிறேன்.
> இந்த பயன்பாடானது கையேட்டின் எல்லா தேவகளுக்கும் பொருந்துமாறு இருக்கும்.
> கவனத்தில் கொள்க : telnet ஆனது ஒரு [flrfc[spec'd networking protocol|854]], மற்றும்
> `telnot` அனது இந்த நெறிமுறையை பயன்படுத்தி வடிவமைக்கப்படவில்லை.

> If you don't have `telnet` installed and don't want to install it, or
> your `telnet` is being picky about connecting to clients, the guide
> comes with a `telnet`-like program called [flx[`telnot`|telnot.c]].
> This should work well for all the needs of the guide. (Note that
> telnet is actually a [flrfc[spec'd networking protocol|854]], and
> `telnot` doesn't implement this protocol at all.)

stream சாக்கெட்டுக்களின் இந்த அதிகளவு தரவு பரிமாற்ற நம்பகத்தன்மைக்கு காரணம் என்ன?
சாக்கெட்டுகள் பயன்படுத்தும் TCP "The Transmission Control Protocol" தரவுபரிமாற்ற நெறிமுறைதான் காரணம்.
இது TCP என்று சுருக்கமாக அறியப்படுகிறது.(TCP பற்றிய அதிக விவரங்களுக்கு பார்க்க) [i[TCP]]
"TCP" ([flrfc[RFC 793|793]]).
நீங்கள் TCP ஐ IP உடன் சேர்த்தவாறு பார்த்திருக்கலாம். இங்கு "IP" ஐ
இணைய நெறிமுறை என்றவாறு அர்த்தம் கொள்ளவும் (பார்க்க [flrfc[RFC 791|791]]).
IP ஆனது முதன்மையாக இணைய இணைப்பின் வழிகாட்டுதலுக்கு பயன்படுகிறது மற்றும் தரவு முழுமைக்கான பொறுப்பேற்கவில்லை.

sockets achieve this high level of data transmission
quality?  They use a protocol called "The Transmission Control
Protocol", otherwise known as [i[TCP]] "TCP" (see [flrfc[RFC 793|793]]
for extremely detailed info on TCP). TCP makes sure your data arrives
sequentially and error-free. You may have heard "TCP" before as the
better half of "TCP/IP" where [i[IP]]  "IP" stands for "Internet
Protocol" (see [flrfc[RFC 791|791]]).  IP deals primarily with Internet
routing and is not generally responsible for data integrity.

[i[Datagram sockets]<]

நல்லது. இப்போது Datagram சாக்கெட்டுகள் எப்படி பட்டது என்று பார்ப்போமா ? ஏன் அவை இணைப்புகளில்லாதவையாக இருக்கிறது?
அவை ஏன் நிலைத்தன்மை இல்லாதவையாக இருக்கிறது ?
சரி இப்போது அவற்றை பற்றிய சில உண்மைகளை நிலையினை பார்க்கலாம்.
நீங்கள் ஒரு datagram ஐ அனுப்பினால் அது வரலாம். அது வரும்போது எந்த வரிசையில் வேண்டுமானால் வரலாம்.
ஆனால் வரக்கூடிய பாக்கெட்டிலுள்ள தரவானது பிழையில்லாமல் இருக்கும்.

Cool. What about Datagram sockets? Why are they
called connectionless? What is the deal, here, anyway? Why are they
unreliable?  Well, here are some facts: if you send a datagram, it may
arrive. It may arrive out of order. If it arrives, the data within the
packet will be error-free.

Datagram சாக்கெட்டுகலானது IP முகவரியை பாதையை தேர்ந்தெடுக்க பயன்படுத்துகிறது. அவை
TCP ஐ பயன்படுத்துவதில்லை; அதற்கு பதிலாக "User Datagram Protocol" (அ) [i[UDP]] "UDP" (see [flrfc[RFC
768|768]]).
Datagram sockets also use IP for routing, but they don't use TCP; they
use the "User Datagram Protocol", or [i[UDP]] "UDP" (see [flrfc[RFC
768|768]]).

ஏன் அவை இணைப்பில்லாதவை? அடிப்படையாகவே UDP ஆனது ஒரு இணைப்பை திறந்து
வைக்காமல் stream சாக்கெட்டுகளில் பயன்படுத்துவது போல செயல்படுகிறது. ஒரு பாக்கெட்டினை உருவாக்கி
அதில் சென்று சேரவேண்டிய IP முகவரி விவரத்தினை IP தலைப்பில் ஒட்டி அனுப்புகிறது.
அகையால் இணைப்பு தேவையில்லை. UDP சாக்கெட்டுகளானது பொதுவாகவே TCP அடுக்கம்
இல்லாத இடத்திலும் சில பாக்கெட்டுகள் தரவு இழப்பு ஏற்றுக்கொள்ளப்படும் இடங்களிலும் பயன்படுத்தப்படுகிறது.  
சில உதாரண பயன்பாடுகள் `tftp` (trivial file transfer protocol, FTP ன் குட்டி தம்பி),
`dhcpcd` (a DHCP client), பல நபர் விளையாட்டுகள், இசை கேட்பது, இணையவழி சந்திப்பு  மற்றும் பிற.

Why are they connectionless? Well, basically, it's because you don't
have to maintain an open connection as you do with stream sockets. You
just build a packet, slap an IP header on it with destination
information, and send it out.  No connection needed. They are generally
used either when a TCP stack is unavailable or when a few dropped
packets here and there don't mean the end of the Universe. Sample
applications: `tftp` (trivial file transfer protocol, a little brother
to FTP), `dhcpcd` (a DHCP client), multiplayer games, streaming audio,
video conferencing, etc.

[i[Datagram சாக்கெட்டுகள்]>]
[i[Datagram sockets]>]

சரி ஒரு நிமிடம்! `tftp` மற்றும் `dhcpcd` அகியவை ஒரு கணினியிலிருந்து மற்றொரு கணினிக்கு
இருநிலை தரவுகளை அனுப்ப பயன்படுத்தப்படுகிறது.தரவு இழப்பு கூடாது என்று சொல்லுகிறீர்கள்.
அதே சமயத்தில் பயன்பாடு வந்தவுடன் அது வேலை செய்ய வேண்டும் என்றும் எதிர்பார்க்கிறீர்கள்.
இரண்டும் ஒருசேர செயல்படும் எந்த வகையான மயாஜாலம் இது என்றே எண்ணத் தோன்றுகிறது.

"Wait a minute! `tftp` and `dhcpcd` are used to transfer binary
applications from one host to another! Data can't be lost if you expect
the application to work when it arrives! What kind of dark magic is
this?"

நல்லது நண்பரே, `tftp` மற்றும் அதைப்போன்ற நிரல்கள் UDP யின் மேலெ தனக்கென்று ஒரு நெறிமுறையை
கொண்டுள்ளது. உதாரணமாக சொல்லப்போனால் ஒவ்வொறு பாக்கெட்டினை அனுப்பும்போதும் பாக்கெட் மறுமுனையினை
அடைந்ததும் "நான் தரவினை பெற்றுவிட்டேன்!" (ஒரு "ACK" பாக்கெட்) என ஒரு பதில் செய்தி அனுப்பிடவேண்டும்.
அனுப்பியவர் எந்த தரவினையும் ஒரு 5 நொடிக்குள் பெறவில்லையெனில் அனுப்பியவர் அதே பாக்கெட்டினை மீண்டும் அனுப்பிடுவார்.
இதே முறையில் தரவு சரியாக சென்றடையும் வரை பாக்கெட்டுகளை அனுப்பும். இந்த பதில் அனுப்பும் முறையானது `SOCK_DGRAM` 
பயன்பாடுகளில் மிக முக்கியமானது. 

Well, my human friend, `tftp` and similar programs have their own
protocol on top of UDP. For example, the tftp protocol says that for
each packet that gets sent, the recipient has to send back a packet that
says, "I got it!" (an "ACK" packet). If the sender of the original
packet gets no reply in, say, five seconds, he'll re-transmit the packet
until he finally gets an ACK. This acknowledgment procedure is very
important when implementing reliable `SOCK_DGRAM` applications.

விளையாட்டுகள், ஒலி அல்லது ஒளி படங்கள் போன்ற நிலைத்தன்மை இல்லாத பயன்பாடுகளில் சில பாக்கெட் தரவிழப்புகள்
அல்லது மிகவும் சாமர்த்தியமாக அமைக்கப்படும் தரவிழப்பு தடுப்பு அமைப்புகள் அதைப் பார்த்துக்கொள்ளும்.
(Quake செயலிகள் இந்த வகையான பிழைகளை _accursed lag_ எனும் சொல் மூலம் குறிப்பிடுகின்றன.
இதில் "accursed" எனப்படுவது எந்த ஒரு மிகவும் எற்கத்தகாத தரவிழப்புகளை குறிப்பிடுகிறது : rephrase latter help needed;) 

For unreliable applications like games, audio, or video, you just ignore
the dropped packets, or perhaps try to cleverly compensate for them.
(Quake players will know the manifestation of this effect by the
technical term: _accursed lag_.  The word "accursed", in this case,
represents any extremely profane utterance.)

என்னதான் தரவிழப்புகளைக் கொண்டிருந்தாலும் ஏன் இதைப் பயன்படுத்துகிறோம். இரண்டு காரணம் : வேகம்! வேகம்!!.
இது வேகமானது அனுப்பு மற்றும் மறந்துவிடு கோட்பாடிட்னை முதன்மையாக கொண்டு இயங்குகிறது அதனுனைய
தற்போதய நிலையைப்பற்றி அதாவது எவையெல்லாம் வந்து சேர்ந்தது எந்த வரிசையில் வந்தது என்றெல்லாம் கவலைப்படுவதில்லை.
நீங்கள் ஒரு அரட்டை செய்திகளை பகிர  TCP சிறந்தது. நீங்கள் 40 இட புதுப்பிப்புகளை உலகத்தின் வெவ்வேறு மூலைகளிலுள்ள
20 ஆட்டக்காரர்களுக்கு அனுப்புகிறீர்கள் எனக்கொள்வோம் அதில் ஒன்றிரண்டு இழப்புகள் சகித்துக்கொள்ளக்கூடியவையே.
அவ்வாறான பயன்பாடுகளுக்கு UDP சிறந்தது.

Why would you use an unreliable underlying protocol? Two reasons: speed
and speed. It's way faster to fire-and-forget than it is to keep track
of what has arrived safely and make sure it's in order and all that. If
you're sending chat messages, TCP is great; if you're sending 40
positional updates per second of the players in the world, maybe it
doesn't matter so much if one or two get dropped, and UDP is a good
choice.

## கீழ்நிலை மற்றும் நெட்வொர்க் கோட்பாடுகள் {#lowlevel}
நான் இதுவரை குறிப்பிட்டது நெறிமுறைகளை எவ்வாறு பயன்பாட்டிற்கு எற்ப அடுக்கி பயன்படுத்துவது என்பதுதான்.
இப்போது நெட்வொர்க்குகள் எப்படி அடிப்படையில் செயல்படுகின்றன என்பதையும்  [i[`SOCK_DGRAM` macro]] `SOCK_DGRAM` 
பாக்கெட்டுகளை எவ்வாறு உருவாக்குவது என்பதையும் காணலாம். எப்படி இருப்பினும் இது ஒரு நல்ல பின்புல அறிவை அளிக்கும்.
நீங்கள் வேண்டுமென்றால் இந்த பகுதியை தவிற்கலாம்.
Since I just mentioned layering of protocols, it's time to talk about
how networks really work, and to show some examples of how
[i[`SOCK_DGRAM` macro]] `SOCK_DGRAM` packets are built.  Practically,
you can probably skip this section. It's good background, however.

![Data Encapsulation.](dataencap.pdf "[Encapsulated Protocols Diagram]")

குழந்தைகளே! _Data Encapsulation_ [i[Data encapsulation]] எனும் அவசியமான ஒன்றை பற்றி
கற்கும் தருணம் இது. இது எவ்வளவு முக்கியம் என்றால் நீங்கள் சிக்கோ மாகாணத்தில் network படித்தால்
நீங்கள் கண்டிப்பாக அதை அறிவீர்கள்.
அடிப்படையில் இது நெட்வொர்க் பாக்கெட் உருவாக்கப்படும்போது அது முதல் நெறிமுறையின் ஒரு தலைப்பு [i[Data enacapsulation-->header]] மூலம் சுற்றப்படுகிறது.
(சில சமயங்களில் [i[Data encapsulation-->footer]] அடிக்குறிப்பும் (footer) முதல் நெறிமுறையின் மூலம் உருவ்வாக்கப்படுகிறது.)
உதாரணமாக [i[TFTP]] TFTP நெறிமுறையை ஐ எடுத்துகொள்வோம்.

Hey, kids, it's time to learn about [i[Data encapsulation]] _Data
Encapsulation_! This is very very important. It's so important that you
might just learn about it if you take the networks course here at Chico
State `;-)`.  Basically, it says this: a packet is born, the packet is
wrapped ("encapsulated") in a [i[Data enacapsulation-->header]] header
(and rarely a [i[Data encapsulation-->footer]] footer) by the first
protocol (say, the [i[TFTP]] TFTP protocol), then the whole thing (TFTP
header included) is encapsulated again by the next protocol (say,
[i[UDP]] UDP), then again by the next [i[IP]] (IP), then again by the
final protocol on the hardware (physical) layer (say, [i[Ethernet]]
Ethernet).

மற்றொரு கணினி ஒரு பாக்கெட்டினை பெறும்போது வன்பொருளானது ஈதர்நெட் தலைப்பை எடுத்துவிடுகிறது kernel அனது IP
மற்றும் UDP தலைப்புகளை எடுத்துவிடுகிறது. TFTP பயன்பாடு TFTP தலைப்பை எடுத்துவிடுகிறது எல்ல தலைப்புகளையும்
எடுத்த பிறகு மீதம் இருப்பது தரவு மட்டுமே.

When another computer receives the packet, the hardware strips the
Ethernet header, the kernel strips the IP and UDP headers, the TFTP
program strips the TFTP header, and it finally has the data.

இப்போது நான் முடிவாக மிகவும் அரிய [i[Layered network model]]
[i[ISO/OSI]] _Layered Network Model_ (aka "ISO/OSI") பற்றி கூற உள்ளேன்.
இந்த நெட்வொர்க் மாதிரியானது பிற நெட்வொர்க் மாதிரிகளை காட்டிலும் பல்வேறு நெட்வொர்க் செயல்பாட்டு அகமைப்பை விளக்குகிறது.
இதை அறிந்து கொள்வதில் பல்வேறு ஆதாயங்கள் உள்ளன. உதாரணமாக நீங்கள் வெவ்வேறு வகையான இணைப்புகளை
(serial, thin Ethernet, AUI) கொண்டிருந்தாலும் இணைப்புகளை பற்றி கவலைப்படாமல் ஒரே மாதிரியாக சாக்கெட் நிரல் எழுதலாம்
ஏனெனில் நிரல்கள் கீழ்நிலையில் (lower levels) நமக்காக எல்லவற்றையும் செய்கிறது.
உண்மையான நெட்வொர்க் வன்பொருள் மற்றும் நெட்வொர்க் வலையமைப்பு சாக்கெட் நிரலருக்கு வெளிப்படையாக இருக்கும்.
  
Now I can finally talk about the infamous [i[Layered network model]]
[i[ISO/OSI]] _Layered Network Model_ (aka "ISO/OSI"). This Network Model
describes a system of network functionality that has many advantages
over other models. For instance, you can write sockets programs that are
exactly the same without caring how the data is physically transmitted
(serial, thin Ethernet, AUI, whatever) because programs on lower levels
deal with it for you. The actual network hardware and topology is
transparent to the socket programmer.

மேலும் தாமதம் செய்யாமல் நான் நெட்வொர்க் மாதிரியின் அடுக்குகளை பற்றி கூறுகிறேன்.
இதை நெட்வொர்க் வகுப்பிற்காக நினைவில் கொள்ளுங்கள்.

* பயன்பாடு (Application)
* காட்சிப்படுத்தல் (Presentation)
* அமர்வு (Session)
* போக்குவரத்து (Transport)
* நெட்வொர்க் (Network)
* தரவு இணைப்பு (Data Link)
* இயற்பியல் (Physical)

Without any further ado, I'll present the layers of the full-blown
model.  Remember this for network class exams:

* Application
* Presentation
* Session
* Transport
* Network
* Data Link
* Physical

இயற்பியல் அடுக்கமானது வன்பொருளை குறிப்பிடுகிறது (தொடர் (serial),ஈதர்நெட், பிற.)
பயன்பாட்டு அடுக்கம்மாணது இயற்பியல் ஆடுக்கத்திலிருந்து ஒரு நீண்ட தொலைவில் இருக்கும் ஒன்றாக நினைத்துக்கொள்ளுங்கள்.
பயனர்கள் நெட்வொர்க் உடன் உரையாடும் இடம்.
The Physical Layer is the hardware (serial, Ethernet, etc.). The
Application Layer is just about as far from the physical layer as you
can imagine---it's the place where users interact with the network.

இப்போது இந்த மாதிரி மிகவும் பொதுவானதாகும் நீங்கள் ஒரு வாகனம் பழுதுபார்க்கும் ஒரு
கையேடாக இதைக்கூட பயன்படுத்திக்கொள்ளலாம். ஒரு அடுக்கப்பட்ட மாதிரி Unixல்
இவ்வாறாக இருக்கலாம்.

* பயன்பாட்டு அடுக்கு (_telnet, ftp, etc._)
* கணினி-கணினி போக்குவரத்து அடுக்கு (_TCP, UDP_)
* இணைய அடுக்கு (_IP and routing_)
* நெட்வொர்க் அணுகள் அடுக்கு (_Ethernet, wi-fi, or whatever_)

இந்த நேரத்தில் அடுக்கங்கள் தரவு அருவமாக்கலை எப்படி கையாளுகின்றன என அறியலாம்.

Now, this model is so general you could probably use it as an automobile
repair guide if you really wanted to. A layered model more consistent
with Unix might be:

* Application Layer (_telnet, ftp, etc._)
* Host-to-Host Transport Layer (_TCP, UDP_)
* Internet Layer (_IP and routing_)
* Network Access Layer (_Ethernet, wi-fi, or whatever_)

At this point in time, you can probably see how these layers correspond
to the encapsulation of the original data.

ஒரு எளிய நெட்வொர்க் பாக்கெட்டினை உருவாக்குகையில் எவ்வளவு வேலை செய்யப்படுகிறது? கடவுளே ! 
ஒரு வேடிக்கைக்காக நீங்கள் "`cat`"! கட்டளையை பயன்படுத்தி பாக்கெட் தலைப்புகளை உள்ளிடுவதை எண்ணிப்பாருங்கள்.
சரி விசயத்திற்கு வருவோம் நீங்கள் stream சாக்கெட்டுகளை பயன்படுத்தும் போது [i[`send()` function]] `send()` செயற்கூறை பயன்படுத்தி தரவை அனுப்புங்கள். 
datagram சாக்கெட்டுகளை பயன்படுத்தும் போது [i[`sendto()` function]] `sendto()` செயற்கூறை பயன்படுத்தி தரவை அனுப்புங்கள் அவ்வளவுதான்.
kernel ஆனது போக்குவரத்து அடுக்கினையும் இணைய அடுக்கையும் உங்களுக்காக சரியான வகையில் பயன்படுத்திகொள்ளும். அஹ்! என்ன ஒரு நவீன தொழில்நுட்பம்.

See how much work there is in building a simple packet? Jeez! And you
have to type in the packet headers yourself using "`cat`"! Just kidding.
All you have to do for stream sockets is [i[`send()` function]] `send()`
the data out. All you have to do for datagram sockets is encapsulate the
packet in the method of your choosing and [i[`sendto()` function]]
`sendto()` it out. The kernel builds the Transport Layer and Internet
Layer on for you and the hardware does the Network Access Layer. Ah,
modern technology.

இதோடு நமது நெட்வொர்க் கருத்த்ருக்களை சுருக்கமாக முடித்துகொள்வோம். ஆம் நான் routing பற்றி கூறவந்ததைதை நான் கூற மறந்துவிட்டேன்.
router ஆனது பாக்கெட்டுகளின் தலைப்புகளை பிரித்து தனது routing அட்டவணையை பார்த்து சரியான முகவரிக்கு பாக்கெட்டுகளை கடத்துகிறது.
இதைத்தவிர எனக்கு கூற அதைப்பற்றி ஒன்றுமில்லை.
இதற்கு மேலும் நீங்கள் தெரிந்துகொள்ள எண்ணினால் [i[Blah blah blah]] _blah blah
blah_ [flrfc[IP RFC|791]]. நீங்கள் அதைப்பற்றி எப்போதும் கற்றுகொள்ளவில்லையேல் நீங்கள் வாழ்வீர்கள்.

So ends our brief foray into network theory. Oh yes, I forgot to tell
you everything I wanted to say about routing: nothing! That's right, I'm
not going to talk about it at all. The router strips the packet to the
IP header, consults its routing table, [i[Blah blah blah]] _blah blah
blah_. Check out the [flrfc[IP RFC|791]] if you really really care. If
you never learn about it, well, you'll live.
