This is a multi user bash Terminal web app done in Ruby using Reel server. This will only run on a linux platform. 

The server code is inside session. demo-app is the example front-end done in Rails that provides the Terminal webpage & also acts as proxy for the commands between the web page & the Terminal server. Typically you need the setup to keep all the user level information at the backend. 

The purpose of this app is not to emulate a true Terminal. It is designed to let website owners give a web based Pseudo Terminal to their users to be able to run linux commands.

> Note : security etc has not been taken care. Google for chroot & /etc/security/limits.conf to be able to jail users & limit their privileges. 

#Installation 

Backup your .bashrc & copy the .bashrc from here to your linux user home directory

	cp ~/.bashrc ~/.bashrc-bkup
	cp .bashrc ~/.bashrc

.bashrc is used to calculate $PS1 . Colored $PS1 might create problems. 

	cd demo-app
	bundle install
	cd ..
	
	sudo su 	#the app needs to run as super user to be able to switch the user as 'su'
	gem install reel
	gem install json
	gem install eye

	cd session/sample
	eye l test.eye
	eye start test

This will run the Terminal server at port 3001. To see if the server has started, just do

	ps -ef | grep ruby

There should be an entry like `ruby reel-server.rb`. It means the reel server is running. 

To stop the server, run 

	eye stop test

Eye is a process monitoring tool http://github.com/kostya/eye . I included it to monitor memory leaks in the server & restart the server if it dies. The logs are in session/sample/eye.log . The server logs is in session/sample/reel-server.log 

This starts the Terminal server. To be able to see the Terminal in action, the *demo-app* directory is included. 

Inside *app/controller/terminals_controller.rb*, change "pocha" to the linux username of your PC

	def current_user
		"pocha"
	end

Open another terminal & run rails server from inside demo-app
	
	cd demo-app
	thin start

> Used thin instead of Webrick as Webrick did not support parallel requests

Now open *http://localhost:3000/* on your browser & start fiddling.

#Test Setup

Stress test the server with multiple users sending requests in parallel through siege
> Install siege from the source.

	cd stress-tests
	# install siege from the source siege-2.55.tar.gz
	sudo su
	ruby multi-user-stress-test.rb #you need to be root as the script creates multiple users on your linux machine & deletes at the end of the tests

Rspec test for multi Terminal per user 

	cd demo-app
	rspec spec/requests/multi_terminals_spec.rb

