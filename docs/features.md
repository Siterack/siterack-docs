# Features

## Overview
**Siterack comes packed with advanced features designed to save you time and keep you focused on the things that matter.**

### Here's a complete list of all core features:


  - <a href="#uptime-monitoring">Uptime Monitoring</a>
  - <a href="#daily-backups">Automatic, Daily Backups</a>
  - <a href="#restorations">One Click Restoration</a>
  - <a href="#safe-updates">Automatic, Safe Updates</a>
  - <a href="#a-i-error-detection">A.I. Error Detection</a>
  - <a href="#malware-scans">Malware Detection and Removal</a>
  - <a href="#health-reporting">Health Reporting</a>
  - <a href="#client-reporting">Client Reporting</a>
  - <a href="#white-labeling">White Labeling</a>




## Uptime Monitoring

Our uptime moniorting system sends intervaled requests (60s) to your websites called 'pings'. This is a simple example script from the Siterack Engine:

``` python
ping = requests.get('example.com')
status = ping.status_code

if status == 200:
    site.ping = True
    site.save()

```

While this ensures that your site is alive and running, Siterack takes it a step further and tries to determine what kinds of errors are causing downtime.

When downtime ocures, Siterack continues to monitor the sites's status and sends an alert via either email or Slack.

#### Slack alert example:

> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/ping.png" style="width: auto;"></img>


## Daily Backups

Siterack automatically runs daily backups of all your sites and securely stores them in the cloud. To accomplish this, Siterack integrates with the popular <a href="https://updraftplus.com/">Updraft Plus</a> plugin. 

Siterack will retain up to 365 backups per site and automatically remove old backups to make room for the new ones. All backups are made available in the site's Control Center.

#### Backup dashboard example:
> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/backups.png" style="width: auto;"></img>


## Restorations

In order to run a restoration, simply choose a backup from the backup dashboard, then click 'Restore' and confirm the order. Siterack will then deploy a worker to complete the restoration in the background while you carry on with other tasks. 

Upon completion, Siterack will let you know via either email or Slack.

#### Slack alert example:
> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/restore.png" style="width: auto;"></img>



## Safe Updates

If allowed, Siterack will automatically check for and run updates on your website's plugins, themes, and WordPress core. This feature can be disabeled in the site's Control Center. 

To ensure the safety of your site, Siterack performs these updates by first running a backup of the entire site. Next, Siterack checks for any <a href="#exclude-packages">blacklisted</a> packages and excludes them from the updates. Siterack then proceeds to incrementally update each package (theme, plugin, or core) while scanning your site for any failures using our <a href="#a-i-error-detection">A.I. Error Detection</a> system. 

If Sitrack detects any problems, it immediately terminates the update process and attempts to restore your site using the most recent backup - i.e. the one made just a few minutes before. If this occurs, you will recieve an email or Slack alert to let you and your team know. 

> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/update_conflict.png" style="width: auto;"></img>


#### Exclude Packages 

To allow for felxibilty, Siterack allows the user to determine which plugins to update and which to keep at their present configuration. To blacklist a plugin or theme, simply navigate to the site's Control Center, scroll to 'Plugins | Themes | Excluded' and click 'Exclude' under each package.

> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/blacklist.png" style="width: 25rem;"></img>



Additionally, Siterack also keeps a detailed log of all completed updates. The logs are searchable and filter friendly so you can find the exact update you're looking for. 

> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/update_logs.png" style="width: auto;"></img>



## A.I. Error Detection

Not to be confused with our <a href="#malware-scans">Malware System</a>, the Siterack A.I. Error Detection system is a novel method for detecting issues and failures within your site. 

The system utilizes nueral-networks to analyze and determine the presence and severity of any issues it finds within the scanned site. 

This feature will continue to be a work-in-progress as more data is made available to refine the algorithm.

#### Example of sigmoid function used in system (in case you're curious).

``` python
def sigmoid(x):
    """
    Takes in weighted sum of the inputs and normalizes
    them through between 0 and 1 through a sigmoid function
    """
    return 1 / (1 + np.exp(-x))
 
 
def sigmoid_derivative(x):
    """
    The derivative of the sigmoid function used to
    calculate necessary weight adjustments
    """
    return x * (1 - x)
```


## Malware Scans

Like most other features, Siterack's Malware Engine runs daily in the background - automatically. Upon the arrival of a new backup, Siterack will make a copy, send the package to the Malware Engine, dissect and scan the files and databases, remove any found malware, record the instances, then compress the clean package and send it to secure, remote storage. 

Once completed, you will see two versions of the same backup in your backup dashboard. The scanned version will be noted by the `_clean` tag attached to the end of the filename. 

If you wish to have the cleaned version installed on your site, simply <a href="#restorations">Restore</a> the `_clean` version of that backup as directed before.

#### Malware Engine
The Siterack Malware Engine is a dual method detection system that utilizes both <a href="http://ijarcet.org/wp-content/uploads/VOLUME-2-ISSUE-6-2037-2039.pdf">signature scanning</a> and <a href="https://en.wikipedia.org/wiki/YARA">Yara rule violations</a>.


##### Example of our Yara implementation:
``` python
def load_yara(self):
    """ 
    Loops through each yara rule in dir, compiles it and loads it. 
    Used to compare against site_files.
    """

    self.infected = False
    def match_callback(data):
        self.infected = True
        return yara.CALLBACK_CONTINUE

    for rule_path in glob.iglob(RULES_PATH + "*.yar"):
        # compile one rule at a time
        try:
            self.rules = yara.compile(rule_path)
            r_name = os.path.basename(rule_path)
            for site_file in self.site_files:
                self.infected = False
                self.rules.match(site_file, callback=match_callback, 
                    which_callbacks=yara.CALLBACK_MATCHES
                )
                if self.infected == True:
                    if site_file not in self.suspicious_files:
                        self.suspicious_files.append(site_file)
                        self.suspicious_files_num = self.suspicious_files_num + 1
                    else:
                        pass
                else:
                    pass
        except:
            pass

```


## Health Reporting

Siterack's Health Reporting system is a useful tool that gives a quick overview of your site's health. The data itself is generated by the WordPress core and is then gathered by Siterack and formated into an easily digestible view. 

It is important to note that the Health system is seperate from the A.I. system. However, the A.I. does incude data from the Health report in it's overall analysis. 

#### Example of the Health dashboard
> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/health_report.png" style="width: auto;"></img>



## Client Reporting

Siterack has the ability to generate client reports for any span of time you need - Whether it's a week or six months. 

To generate a report, simply click 'Build Report' in your site's Control Center. Then pick the dates you want to report on, what aspects you want included, and add a personalized note. When complete, click 'Generate Report' and check your email for the report. 

***Please note that the report generation may take a few minutes to complete.**

Oh yeah, and the reports look pretty good... (if we do say so ourselves :wink:)

#### Sample Report Cover

> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/report_example.png" style="width: auto;"></img>




## White labeling

As expected, the entire Siterack Engine is a white-labeled-ready product. You are free to use the software as a resellable service to your clients and customers - no copyright issues! 

Also, for each Client Report, your company name and logo will be displayed on the bottom left corner of the cover page.

##### Logo and company name placement
> <img src="https://storage-siterack.sfo2.cdn.digitaloceanspaces.com/static/dashboard/img/illustrations/white_labeling.png" style="width: auto;"></img>

