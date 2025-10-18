Game of Active Directory - Notes

This is an intentionally vulnerable Active Directory lab in the theme of Game of Thrones. It's a massive effort put out by the folks at Orange Cyberdefense and is a little finicky to setup on some hardware. I ran into an issue with installing it properly in VMware Workstation Pro. I never did resolve the issues I had which blocked me from installing. I, instead, grabbed a friend and reached out to the cloud hosted lab purveyors on Parrot-CTFs. I had questions that were easily answered by their Discord support team and have been more than happy tinkering about with a friend of mine on our own, separate instances.

This will house my notes on the progression through the lab. I would highly recommend following the creator, Mayfly277's blog: https://mayfly277.github.io/ for a baseline of what you're doing. Be prepared to research on your own as the blog is mostly a walkthrough for the few pages I've engaged with so far. You want to be able to understand **why you're doing what you're doing** for real life engagements, you want to know how an ADCS attack is carried out and the **why** behind the attack so you can teach your peers. This field is nothing without daily, even hourly, learning and having someone explain it fluently is a massive bonus.

# Initial nmap scans
## 