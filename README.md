# Global Entry Appointment Cancellation Checker #

**NOTE:** If you find this tool useful, please star this project!

This allows you to check and set up notifications for Global Entry enrollment appointment cancellations through the [Global Online Enrollment System website](https://goes-app.cbp.dhs.gov/). It uses [PhantomJS](http://phantomjs.org/), a headless browser, to log in and report back the first available open appointment. If one is found sooner than your current appointment, it will optionally schedule the sooner appointment and notify you via email.

Once setup, you can check to see if there is a sooner one than already scheduled with the following commands:

	phantomjs [--ssl-protocol=any] ge-cancellation-checker.phantom.js [-v | --verbose] [-s | --schedule]
	./ge-checker-cron.py [-s | --schedule] [-r | --repeat]

You can also schedule appointments by adding [-s | --schedule] to either command. NOTE: The phantom.js command will only write to a log file/the console. It will not notify you via email that your appointment has been updated.

## Setup ##

Please be aware that you must first sign up and schedule an enrollment appointment via the [GOES website](https://goes-app.cbp.dhs.gov/) before using this script. Note that because this navigates the GOES website like any other browser, any updates to the GOES website may break it.

### Dependencies ###

The following must be installed and available in your `$PATH`:

* [PhantomJS](http://phantomjs.org/)
* Python (2.7)
* [Sendmail](http://en.wikipedia.org/wiki/Sendmail)

**Note:** Many ISP's block port 25, used for email. If you want to set up email notifications for appointment cancellations and your ISP blocks port 25, a good way around this is to [set up Sendmail to send email through Gmail](http://linuxconfig.org/configuring-gmail-as-sendmail-email-relay). If you don't care about emails, you can also just track the log file using the following command:

        tail -f <logfile_name> &

### Configuration ###

To get started, copy `config.json.example` to `config.json`. In your new config, fill out the following settings:

* **logfile**: the full path to a logfile. New cancellations, unsuccessful checks, and errors are logged in this file, if provided.

* **email_from**: the "from" address for the notification email

* **email_to**: a list of addresses to send the notifiation email to (must be an array)

* **period**: minutes between periodic checks for new appointments

* **username**: the username to log in with on the GOES website

* **password**: the password to log in with on the GOES website

* **enrollment_location_id**: the value of the enrollment location that you must choose when setting up your appointment. After choosing from the dropdown, find this ID by running this in the console:
    
        document.querySelector('select[name=selectedEnrollmentCenter]').value

* **init_url**: the login page of the GOES website.

Please also ensure you are the only one with access to your `config.json` to protect your username and password.

### Scheduling ###

If you'd like to be notified of cancellations regularly, you can run the command with the [-r | --repeat] argument. The following runs every half hour:

    ./ge-checker-cron.py -r &
    tail -f <logfile_name> &

Of course, please make sure that [SendMail is working](http://smallbusiness.chron.com/check-sendmail-working-not-linux-49904.html) before trusting this to notify you. If you want to keep a record, be sure to set a logfile path in your `config.json`.

## Contribution ##

If you're interested in contributing, here are a few feature ideas that might improve this:

* Fix email sending so it isn't so impossible to set up
* Specify acceptable ranges of times and dates in config.json
* Error handling: Figure out how to stop the script after it fails the first time
* Allow for accounts that haven't scheduled their initial appointment yet
* Come up with some non-workaround way to implement verbose in the python script (i.e. print the log)
* Figure out a way to run this on the cloud (i.e. not require laptop terminal)
* Have the script dynamically ask for your credentials
* Add install logic (i.e., automatically intall packages that aren't present)
* Plugin system: update `ge-checker-cron.py` to allow configuration for running multiple phantom scripts and keeping track of multiple appointment systems (not just a Global Entry cheker - e.g., the DMV).
