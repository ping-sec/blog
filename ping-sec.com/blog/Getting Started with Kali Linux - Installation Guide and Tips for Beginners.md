![[kali-linux-1080x675.webp]]
## Introduction

Kali Linux is often used by cybersecurity professionals, ethical hackers, and IT security specialists to test the security of systems and networks, as well as by educators and students in cybersecurity courses. While it is a powerful tool, it’s essential to use it ethically and legally. Because: Federal Prison is a very real place you can end up.

## How is Kali Linux Used?

Kali Linux comes with a wide range of security and hacking tools pre-installed. These tools cover various aspects of information security, such as network analysis, web application testing, password cracking, reverse engineering, exploitation, and wireless attacks. Kali Linux allows users to customize and add additional tools and features based on their needs. This makes Kali Linux highly versatile for different security tasks.

Kali Linux supports a wide range of hardware platforms and is available for ARM-based devices, virtual machines, cloud environments, and more. Kali Linux has comprehensive documentation and a strong community of users who contribute to its development and support.

In addition, Kali Linux uses a rolling release model, meaning that the distribution is continuously updated with the latest tools and features, allowing users to have the most up-to-date software. Since Kali Linux is primarily designed for security professionals, Kali Linux focuses on security and privacy. It runs in a non-root user mode by default, and users are encouraged to use strong passwords to encrypt their data.

## 3 Tips to Remember Before Installing Kali Linux

There are a couple of things to keep in mind before we start downloading and installing Kali Linux. The first is: Always, ALWAYS run Kali Linux in a VM. While Debian is a stable base operating system, you can never know with Kali Linux if any of your tools might suddenly break. It will be hard to get back on track if your network drivers suddenly get corrupted.

The next, we covered slightly already: Kali Linux is best used ethically and legally. Anything and everything you do as a penetration tester, bug bounty hunter, or threat researcher needs to be up to legal snuff because running outside of the law could land you in Federal prison.

Third, we’re going to soup up our Kali with a special bag of tricks called PimpMyKali. It is a utility script maintained by Dewalt, one of the TCM Devs. It’s a great tool meant to clean up some of the more fiddly messes that plague Kali, not to mention, making life easier for students taking the TCM courses.

## Download Kali Linux

So, now we know why we want to have Kali in our tool shed: It’s time to download it for real. Head over to [https://www.kali.org/get-kali/#kali-platforms](https://www.kali.org/get-kali/#kali-platforms). You’ll see a Choose Your Platform banner at the top of the page, MOST people will need what is under the tab marked Virtual Machines. We won’t cover the other options there, but you can read the short descriptions if something else would work better for you..

For most people, the Virtual Machine section is what they need. Once you’ve clicked on the option, the banner at the top will change over to Pre-built Virtual Machines. There is also a toggle for 64-bit and 32-bit platforms. The top four choices are what’s recommended. There are VMware, VirtualBox, Hyper-V, and QEMU editions available for downloading. Take heed these are large, 3-4 GB files, so if you have a bandwidth cap you may want to plan accordingly.

Beneath the top four options, there is a weekly build that’s better suited for those who want to receive less frequent updates. This can circumvent the rolling release cycle for those with lower bandwidth caps. 

## Installing Kali Linux

Next, we will walk through the installation process for Kali Linux.
![[kali-linux-1.webp]]
![[kali-linux-2.webp]]

Above are a few photos of the download process in Firefox. I’ll be using VirtualBox for the install, but the process is very similar for VMware.

Next, we fire up VirtualBox:
![[kali-linux-3.webp]]

I have my Kali 2024 and my OSINT VMs showing here, but ignore them and we’ll click the giant green “+” sign that says Add:

![[kali-linux-4.webp]]

Two clicks on the vbox file listed above and you’re golden, it should show up as a selection:

![[kali-linux-5.webp]]
## Installing PimpMyKali

We could stop once we have VirtualBox and Kali running, but we’re hackers and we can’t leave anything alone, right? Next, we’re going to enhance our Kali Linux installation with a custom tool that our very own Dewalt created and maintains: PimpMyKali is available on Github.

Let’s sign in to Kali using the default username of “kali” and password of “kali”.
![[kali-install.webp]]
Once we’re logged in and see the desktop, let’s open Firefox and find the [PimpMyKali repository.](https://github.com/Dewalt-arch/pimpmykali)

![[pimpmykali.webp]]
Typically, we’d need to download the .sh script and make it executable, but Dewalt has programmed this bad boy to run without that step. Every little bit of time savings helps!

Now what we need to do is open a terminal on our linux desktop:

![[pimpmykali-2.webp]]


We click the small link next to the Firefox icon above and we next type “git clone https://github.com/Dewalt-arch/pimpmykali”. 

![[pimpmykali-3.webp]]
Hit enter and the script is downloaded. We need to navigate to the proper directory, so we do that by using the change directory or “cd” command.

![[pimpmykali-4.webp]]
Once you’ve done the ‘cd’ and you’re in the proper directory, we run the next command:

sudo ./pimpmykali.sh

![[pimpmykali-5.webp]]

Once you’ve got the command in place and running, you’ll see the main menu for PimpMyKali:

![[pimpmykali-main.webp]]
Look at all the options available to us! For our purposes, we are just going to install option N for a New VM Setup. After hitting N and letting it spin for a while, we see:
![[pimpmykali-setup.webp]]
At one point, you’ll see some red text that is a warning and asks if you want to re-enable the option to login as root:
![[pimpmykali-root.webp]]

Most people will not need to run as root, so you can just hit ‘N’ here and wait for the confirmation that it has completed.
![[pimpmykali-confirmation.webp]]
## Conclusion

And that’s it! You’ve installed Kali Linux using a VM and added the PimpMyKali tool. Now you are ready to start your ethical hacking journey. Through this blog post we learned that we should only use Kali Linux for ethical and legal purposes, that we only want to install Kali Linux in a VM and we walked through the process of installation. If you need more guidance on how to use Linux, check out our FREE [Linux 100: Fundamentals](https://academy.tcm-sec.com/p/linux-fundamentals) course on TCM Security Academy. This video course will walk you through all you need to know about the Linux operating system.

Happy Hacking!