# Full Node.js Deployment - NGINX, SSL With Lets Encrypt

https://youtu.be/oykl1Ih9pMg  

Hey what's going on guys in this video
I'm going to show you how to fully
deploy an ojs and Express application
and we're also going to use nginx as a
reverse proxy so that we can access it
through port 80 which is the port for
HTTP and then in addition to that we're
going to add a domain name and we're
going to create a or install an SSL
certificate using let's encrypt a free
SSL certificate all right so we're gonna
do quite a bit here and we're gonna be
using digitalocean as our host it's it's
a cloud hosting service service that I
use all the time that I highly recommend
and they have a gist file in the
description that has pretty much all the
stuff that we're gonna be doing along
with the commands and then there's also
a link to get $10 free from digitalocean
so you don't have to pay anything - if
you want to follow along and do this
with me alright so the app we're gonna
deploy is very very simple you can see I
already have it on github - just called
it node app and it's just a single
app.js file in fact I'll show it to you
in vs code so basically it's just the
simplest Express application you could
build we just brought an Express created
a route that outputs an h1 and it
listens on port 5,000 and if I want to
run it locally I can just say node and
then app J s which is the file name you
can see it's running and I can run it on
my local host and there it is okay so
nothing spectacular but obviously you
would replace this with your application
so this is just for an example so I'm
just gonna stop that alright now of
course you want your your code on github
either as a public or private repository
because that's how we're gonna get it on
to our server so I'm going to jump over
to digitalocean and you can sign up you
can use that link that that's in the
gist and you'll get $10 free so once you
sign up you can create a droplet and I'm
gonna choose Ubuntu here 18.04 is my
operating system and I'll choose the $10
plan here different plans have different
you know CPU and disk size and stuff
like that
and then let's see we'll just leave the
default region as far as authentication
you can use either a one-time password
where you can use that I think they'll
email it to you and then you can change
it or you can set up some SSH keys which
I already have set up I'm not gonna go
over the process of generating SSH keys
I do have an SSH crash course if you
want to check that out if I remember
I'll put the link in the description but
I'm just gonna use my iMac SSH which is
what I'm on now and then just change the
hostname to let's say node app and
that's it we can just go ahead and
create our droplet if you're not
familiar with digitalocean a droplet is
just a server it's just like it's just
like an empty Linux server that you can
install absolutely whatever you want on
and I've done a bunch of videos
deploying to Heroku which is a great
service as well it makes things much
easier however you don't have the type
of freedom you have with you know your
own Linux server like this alright so
that's set up now we have our IP address
right here for our droplet so I'm going
to copy that and open up a terminal and
I'm gonna go ahead and SSH on sshh but
SSH and I'm gonna use the root user at
and then the IP address okay so we'll
just say yes to this and then we should
get logged in alright so you can see now
we're logged into our server or our
droplet so I'm just going to clear this
up now if you're actually pushing to
production you know you're building a
real application deploying it I would
suggest creating a new user not using
the root user however I'm just gonna use
root for our convenience and just to
save some time okay so first thing we
want to do is update our packages so
let's do sudo and I know I'm using root
so I don't actually have to use sudo but
you know it's just in case or not I'll
just use it anyway so I'll say sudo apt
update which is going to update our
packages and then after that we want to
install node because we don't have node
installed by default if I say no - -
version you'll see that it's not in
and it tells us to run app to install
node.js now before we run this in order
to get the latest version we actually
need to add the repository so if we go
back to that gist file I actually have
the link right here and this will allow
us to install node version 12 which is
the latest version at this time so I'm
gonna grab that okay so it's a curl
command we're gonna paste that in and
run it and then we should be able to
install the latest version of node by
doing sudo apt and again I know I don't
have to use sudo here so sudo apt
install and then nodejs yes and what
this will do is it'll install node on
our server and NPM so if I clear this
out and now I do
NPM - - version you can see we have that
installed and we also have node
installed I'm just going to make this a
little bigger yeah all right so we'll
clear that up so now we have NPM in node
installed on our server so the next
thing we want to do is get our
application on to our server so usually
what I'll do is CD in to our home
directory and then create like an apps
folder so we'll CD into apps and then
from here I'm gonna run get clone and
then go to my github where I have my
application and right here where it says
clone or download you can use SSH but
you'll have to set up SSH keys on your
server or with github so I'm going to
just use HTTPS so I'll just copy this
link here and then we'll go over and
just paste that in and run it and what
should happen well first we have to
enter github info because I didn't use
SSH
okay so now if I do an LS which will
list the directory contents you'll see
that we have node app so I'll CD into
node app and if I do an LS here you'll see
that we have our application files now
we don't have our dependencies installed
notices no node modules folder so we
have to run npm install and that'll look
at the package dot jason and install
whatever dependencies there are in this
particular application we only have
express as a dependency alright so so
now that we have our dependencies
installed we we should be able to run
the application with actually I don't
think I have a start script so we can
just say node and then app dot J s which
is the name of the entry file and you
can see it's listening on five thousand
and at this point we should be able to
copy our IP address for the droplet and
we should be able to go to ports five
thousand and there it is so it is live
right now but it's still it's not set up
the way we want it to be obviously we
don't want people to go to port five
thousand so that's where nginx comes in
but before we do that we have to install
something called PM - so let me just go
to the PM - website here so PM 2 is a
process manager basically it's going to
run our node application in a production
environment as a process as a background
process instead of having it just linger
in the terminal like this obviously this
is an you know we don't want to keep it
like this so I'm going to stop this with
ctrl C and the way that we install PM 2
is with NPM so we're gonna say npm
install and we want to install it
globally so - G and then PM - all right
and then we can use PM 2 to start our
application and manage it alright so
clear that up and while we're inside the
application folder let's run PM
two starts and then the name of the file
so basically instead of running node app
KS we're running p.m. to start app Jas
and you can see that now our application
is running okay so it's online it shows
the CPU it's using memory and stuff like
that and the terminal is freed up so
basically it's just running in the
background and it should still work if I
go back to that IP address and reload
you can see our app is is running all
right now there's different commands we
have like p.m. to status and you can
look all this up in the documentation
this will show you all your apps that
are running if we wanted to let's say
restart our application we could do p.m.
- and then app or we want to do p.m. to
restart app j/s and that will restart it
we could do p.m. to stop app j/s and
I'll have a bunch of commands in that
gist file we can also look at logs so we
can do p.m. to logs and what this does
is it actually just kind of runs a
stream of logs this app listening on
port 5,000 comes from this right here so
any console logs are going to show up
here and then we can get out of this
with control C and if we want to flush
our logs or clear our logs we can do
p.m. - flush and now if I do p.m. to
logs you'll see that they're the logs
have been flushed okay
so just some of the commands that you
can use now we want to make sure that
our application goes back up if the
server reboots so there's a command that
we have to run for that and that's p.m.
to startup abun - alright so now if you
look right here what it did is it made a
script for booting at startup so even if
I reboot the server like we can say
reboot it's gonna close our connection
because the droplet is actually
rebooting and it might take a second
before we can go back in but once we go
back in it should still be running so
let's go ahead and try
it's okay and now if we do p.m. to
status our application is running and I
can go over here and reload and it's
still running all right so we have that
part taken care of now before we get
into nginx I would suggest enabling the
firewall so if we do ufw status you'll
see that it's inactive so I'm actually
going to enable the firewall I'm gonna
say ufw enable so it's going to say may
command may disrupt existing SSH
connections I will say yes and then
we're going to allow SSH by saying ufw
enable SSH
I'm sorry not enable allow allow SSH
okay so now if we do ufw which is our
firewall and we check out status you'll
see that port 22 is open it's allowed
because that's the port for SSH which is
what we're using right now okay now if I
go back to the browser and reload it's
not good it's no longer gonna work
because port 5000 is closed in fact
everything is closed right now we have
our firewall up all right so I don't
even want port 5000 to be to be able to
be accessed we're gonna set up nginx so
that it goes through port 80 which is
the standard HTTP port so what we do
want to do is allow port 80 which we can
just actually just say HTTP and then we
also want to allow HTTP which is port
443 so if I do ufw status you can see
that now port 80 and 443 are allowed ok
so now that we've done that we've set up
a basic firewall and there's a lot more
security things you can get into but
that's some you know I'm not going to
get into those specifics now we want to
do a set up nginx so that we can
actually access our application with
just this IP like this ok this should be
able to access our
so to do that we need to install nginx
so we're gonna say sudo apt install
nginx and for those of you that aren't
familiar with nginx it's a web server
it's it's similar to Apache if you've
ever dealt with Apache we're gonna do a
little configuration but not not much at
all
so nginx is now installed what we want
to do in fact if I reload you'll see it
will say welcome to nginx it's just kind
of the landing page what we want to do
is go to the gist here and we want to
edit let's see so we're right here we
just installed nginx now we want to edit
this sites available slash default file
and you can open this with any editor
I'm using Nano if you want to use vim
that's fine as well Nano is a lot easier
to use for those of you that haven't
that haven't don't have experience with
vim so I'm gonna go ahead and paste that
in and run it it's going to open up the
file and we just want to go down a page
down to the server block here so we have
this listen on port 80 we're gonna leave
all this stuff and go down to this
location area right here so in this
location let's see I'm just gonna I'm
gonna get rid of this and then if I go
back to the gist file you're gonna see
this right here location and we want to
put this stuff in that location block
and this is where it's it's basically
setting up that reverse proxy so that
when we go to port 80 it's gonna load
our app that's running on port 5000 on
our local machine which is good which is
obviously our server so I'm gonna copy
this stuff that's in the location and
then I'm just gonna go ahead and we
should be able to just paste that in
right here server name we you can leave
it like this but if you're gonna use a
domain name you want to put that here so
I'm gonna be using Travis II media dot
site as my domain just for this video
and then I also want to put the www
version
okay you don't want to use this you want
to use whatever domain you want to use
if you want to use a domain alright so
that should be all we need to do here so
I'm gonna go ahead and command our
control X and it's gonna ask if we want
to save I'll say yes enter and now that
should be saved now we can check and
make sure nginx is is okay by saying
sudo and Gen X - T and it says that the
configuration file is okay test is
successful all right so everything
should be okay now we want to restart
the service so we're gonna say service
nginx
restart okay so that should restart it
now let's go back to the browser and
let's reload our IP address and there it
is as our node application running on
port 80
it's running on five thousand but we're
using nginx basically in front of it as
a reverse proxy to to access it through
port 80 okay we can't even go to port
5,000 it's just gonna you know it's not
gonna work all right so that's setup so
now we want to add the domain and like I
said I'm gonna be using Traverse
immediate site which I have registered
right here at Namecheap Namecheap is is
a great service I suggest for
registering domains I actually have an
affiliate link right here if you're
interested if you're gonna buy a domain
that'll give me a little kick back I'll
appreciate that basically before we do
anything here with a Namecheap we need
to go to digitalocean
and go to over here on the side
networking and we want to add the domain
whatever domain you're gonna use you
want to add here so I'm gonna put in
Travis e-media
site and add domain okay now there's two
things we want to add here we want to
add to a records for this domain so one
is for the root which we use a an @
symbol for and we can choose a droplet
that we want this to basically point to
and we're going to use our node app so
we're going to create that record
and then we also want the WWV version
2.2 note app create the record okay so
that's all we have to do on the digital
ocean side now on your registrar whether
it's Namecheap or I don't know GoDaddy
or wherever you register your domains
you're gonna want to add the
digitalocean name servers so name cheeps
pretty easy you have this area right
here by default it should say basic DNS
but we want to choose custom DNS and we
want to add NS 1 . digitalocean comm for
the first one and then i'm just going to
copy this we want three of these but we
want NS 1 and then n s 2 and then NS 3
and then we'll click the little green
checkmark here ok so that should change
the name servers now this might take a
little while to propagate so it's it can
be different all right here it says up
to 48 hours to take effect usually it's
not that long sometimes it's right away
I don't really know what you know what
causes it to take a longer amount of
time or not but let's go ahead and just
see what happens if we do go to Travis
su media dot site probably nothing yeah
so it's just the name cheap alright you
know what I'm gonna do guys is
you know we're gonna do is just pause
the video and wait until this propagates
hopefully it's not 48 hours I have a
feeling it'll be like 10 minutes or so
but I'm gonna pause the video and when
this propagates I will come back and
then we'll go ahead and do the SSL
certificate alright guys so luckily that
didn't take too long was about 15
minutes or so so you can see that
travesty media dot site is now going to
the note application if I use the www
version same thing
alright so our application is deployed
now we just want to add an SSL using
let's encrypt which is pretty easy so
let's jump in back into the terminal
here make sure you're in your server and
if you look at the gist file I have all
the commands laid out for you so right
here basically we want to add the PPA so
we want to be able to add the repository
here so I'm gonna copy this and I'll
just paste that in okay it's go ahead
and hit enter here and then once that's
done let's just run an update so sudo
apt update okay so now we want to
install let's see right here we want to
install this Python cert bot nginx so
we're gonna grab that and paste that in
yes okay once that's done we can go
ahead and add our certificate now you're
gonna want to use whatever domain you're
using so let's clear that up and I'm
just gonna change to change these
see media dot site and let's also change
this dot site okay so I'll run that it's
gonna ask for an email address and we
want to agree to the Terms of Service
we'd be willing to share your email
address let's say no okay so it's
obtaining a new certificate waiting for
verification cleaning up challenges as
you can see how easy this is deploying
certificate let's see so this is where
we want to decide if we want to
automatically redirect to HTTPS or not
I'm gonna say yes so I'm going to choose
option - meaning that it'll always be
HTTPS so now let's go back to our site
and reload and now you can see it no
longer says not secure we now have a
secure connection we have a valid
certificate so it's as easy as that now
these certificates will expire automatic
spire automatically after 90 days but
there is a command we can run to
basically test the renewal process so we
can do that with start thought and we
want to do renew and let's do - - dry
run okay all right I should say it'll
automatically renew after 90 days but
we're gonna test that here so renewing
existing certificate performing changes
waiting for verifications basically it's
doing the same thing and this should run
every 90 days so we just kind of did a
simulation of a renewal congratulations
all renewals succeeded alright so that's
it we now have a node.js application
on running on digitalocean we use PM 2
as our process manager there's other
ones as well you have forever
I prefer p.m. - then we used nginx as a
reverse proxy so that we can access it
through port 80 then we added a domain
name and then we add an SSL with let's
encrypt and everything I did is in this
gist file so you can kind of use this as
a guide for future projects alright
thanks guys hopefully you enjoyed this
and I'll see you next time.
