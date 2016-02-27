# Ugly-Framework-Yo-JSS

Shea Craig's [Yo application] (https://github.com/sheagcraig/yo) as well as Julien Blanchard's [Terminal Notifer](https://github.com/julienXX/terminal-notifier) are great tools, but both share a common problem.  How the heck to you get a notification to run from a Casper policy? The security frameworks in OS X make this difficult.  Since a casper policy runs as root, the security frameworks do not let that process make the right socket connections to the logged in user's GUI.

To "hack" this (for lack of a better word), I've used the method below.  The origins of this method were built by myself and my good friend Nathan Boggs.  Since then, I've developed it a little more.  I really am not happy that we have to go to these lenghts, but it is what it is.  

A more proper "solution" would be writing a privileged helper tool for Yo and/or Terminal Notifier.

### Components of this very ugly "solution"

##### The application itself (Yo or Terminal Notifer)
The app can be branded as your own app or vanilla from repsective repos.  It can be installed anywhere. Recommend Applicaionts, Utilities, or Application Support

#####  A preference file that holds values for the notification to be sent
e.g. /Library/Preferences/com.myorganization.notification.plist

#####  A trigger file
Hidden file that simply gets "touched" by the casper policy to launch the notifiction

#####  A launcher script that lives on the local system
- Reads the values from the preference file
- Executes the notification app using those values to display desired message

#####  A user level launch agent
Watches the trigger file.  (Watch Path)

#####  A script that runs as a Casper Policy
- Sees if a user is logged into GUI first.  Exits if no.
- Sees if components above are installed and in right locations
- Makes sure launch agent is running
- If conditions not met, runs a "self heal function" - a "jamf policy -event customtrigger" command to reinstall the needed components
- Writes values for notification to plist
- Touches the trigger file

