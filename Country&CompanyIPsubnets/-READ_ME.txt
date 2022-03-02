#Once you create the firewall Address-List using the country/company files, what you do with the list is up to you. 
#You can block all attempts against your router(input rule)
#or you can block all attempts thru your router(forward rule). 
#An example of input rule is the default port used for WinBox. Maybe you
#want to ONLY access your MikroTik from within the United States, or Canada, or England. Using the prebuilt address lists, you can possibly lock down
#where your MikroTik is geographically available from, or lock down your port-forward rule to your camera system so it's only available from (example country).
#Building the firewall rules that use Address Lists is outside the scope of this section.
#If you made it this far, it is assumed you know what a firewall rule is and you know how to build one.

#Also, most of the country lists were built from this website, hats off to you sir.
https://mikrotikconfig.com/firewall/
#Here is another site to build a country list
https://www.ip2location.com/free/visitor-blocker


#Fine, I lied, here is an example of some firewall rules to use the address lists

##This setup allows you to block countries by their IP address.
##At this time I have NOT added all the countries together into one
##list because it would probably crash your Mikrotik.
##So the rules and examples I give you will be by country ONLY.

##There are two rules for each country. One to prevent their traffic
##passing through your router, and one to prevent their access 
##to your router itself.
##Enter the below into your terminal window in your Mikrotik.
##(Tested on Winbox v6.27)

/ip firewall filter
add action=drop chain=input comment=\
    "Block China's access to your router" src-address-list=\
    China

/ip firewall filter
add action=drop chain=forward comment=\
    "Block China's access through your router" \
    src-address-list=China


##Those two rules in themselves will NOT block China from getting through your router. It just sets up rules to look for 
##address lists labeled "China" in IP/Firewall/Address Lists
##and then block every IP labeled "China".
#At this time, I would NOT recommend getting too eager with blocking lots of countries THRU your router (chain=forward rule).
#Even Microsoft uses resources outside the USA, so if you block Amsterdam from passing thru your router,
#Windows Updates may no longer work. You have been warned.

##To add China's IP's to your Address List, go into the link on this page
##marked "Country&CompanyIPsubnets" and copy and paste the contents of
##China (or any other country) into your terminal window.
##Example below from China.

/ip firewall address-list
add address=1.80.0.0/13 list=China
add address=1.92.0.0/14 list=China
add address=1.192.0.0/13 list=China
add address=1.202.0.0/15 list=China
add address=1.204.0.0/14 list=China


#There is an additional rule(s) you can add to the Raw tab of your IP/Firewall section, but be careful.
#This rule has to do with prerouting. Because it (kind of) gets blocked before it even hits your firewall it will be hard to Torch/track using your MikroTik.
#I would recommend using this rule ONLY if you know what it is doing.
#The first rule is an allow rule so your router can try and pull updates. The second rule blocks all listed Address Lists traffic from passing into/thru your router.

/ip firewall raw
add action=accept chain=prerouting comment="Allow MikroTik" src-address-list=\
    Mikrotik
add action=drop chain=prerouting comment=\
    "Block Infected networks into my router from Address Lists" \
    src-address-list=Bad-Infected_Networks

#Obviously you will need Address Lists that match both entries so it can allow MikroTik into your router and also drop traffic on the second rule.
#Here is a sample of the MikroTik addresses I allow thru my MikroTik.

/ip firewall address-list
add address=cloud.mikrotik.com list=Mikrotik
add address=cloud2.mikrotik.com list=Mikrotik
add address=download.mikrotik.com list=Mikrotik
add address=download2.mikrotik.com list=Mikrotik
add address=mikrotik.com list=Mikrotik
add address=www.mikrotik.com list=Mikrotik
