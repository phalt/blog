There is this running joke at work where, if you see a computer unlocked and unattended, you go and write messages about Justin Bieber in slack on their account:

![https://i.imgur.com/q4GfQT6.png](https://i.imgur.com/q4GfQT6.png)

At first this didn't bother me too much - people like to have fun and mess around. Sometimes someone would take it a little bit further and write a full blown love note or a poem. It gets quite entertaining.

But then I started to realise something serious: **all these people were leaving their computer unlocked and unattended**.

We've all heard to stories, [as far back as 2008](http://www.dailymail.co.uk/news/article-1082402/Tax-website-shut-memory-stick-secret-personal-data-12million-pub-car-park.html) (sorry for the Daily Mail link) of poor security practices resulting in companies being bankrupt through loss of confidence or loss of data. Things like this are happening more and more often. This year [the UK's NHS suffered a catastrophic hack](http://www.telegraph.co.uk/news/2017/05/12/nhs-hit-major-cyber-attack-hackers-demanding-ransom/) in part due to poor infosec practices.

It worries me that people aren't educated at work places about even _basic_ security practices.

Consider it this way: if you work in a startup then often the reason you're going to beat competitors is because you're working on something special that will change the game. If your staff are leaving their laptops unlocked and unattended, what will happen if they do that in a café and someone steals their laptop? You lose your edge, your company goes under, all because you didn't educate your team on good security practices.

---

I've been researching infosec and cryptography over the past few months. I've got PGP keys set up, two factor authentication, even a [FIDO key](https://www.yubico.com/) for some applications I want to keep extra safe.

What I've done is a bit over the top. But what you need to do to have even the _Minimum Viable Security_ on a Macbook is miniscule. It takes **two minutes to set up**.

This setup puts your machine to sleep whenever you leave it unattended and requires you to enter a password when you wake it back up. Simple security that will stop 99% of would-be hackers or thieves in their tracks.

# Set it up

1) Go to System Preferences.
2) Click on Desktop and Screensaver:

![https://i.imgur.com/4JxCRK5.png](https://i.imgur.com/4JxCRK5.png)

3) Click on the "Hot Corners..." button on the Screen Saver tab.

4) Pick "Put Screen to Sleep":

![https://i.imgur.com/DZNK7Lu.png](https://i.imgur.com/DZNK7Lu.png)

5) Save it.
6) Go back to System Preferences.
7) Go to Security & Privacy.
8) Change "Required Password..." to _"immediately"_ after sleep or screen saver begins:

![https://i.imgur.com/bu92GKC.png](https://i.imgur.com/bu92GKC.png)

You're done!

What does this do exactly?

With this setup you swipe into one corner of the trackpad and the screen will go to sleep, locking your computer immediately in the process. I recommend picking the corner that you use the least (and it takes a minute or two to get used to).

With this you avoid the excuse of leaving your machine unlocked when you leave it unattended, because it only takes a single swipe to lock it.

 It's not the best security you can have (you should [encrypt your MacBook harddrive](https://support.apple.com/en-us/HT204837) and set up your own [PGP keys](http://www.bitcoinnotbombs.com/beginners-guide-to-pgp/) and [always use a VPN](https://www.expressvpn.com/what-is-vpn), but those are for another blog) but it is a better security practice than 90% of other people out there.
