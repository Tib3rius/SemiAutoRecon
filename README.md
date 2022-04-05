> It's like bowling with bumpers, but you have to verbally agree to every throw. - [@ippsec](https://twitter.com/ippsec)

For a quick introduction to SemiAutoRecon, please see the below YouTube video:

[![SemiAutoRecon YouTube Video](https://img.youtube.com/vi/fy4WWszV9Oc/0.jpg)](https://www.youtube.com/watch?v=fy4WWszV9Oc)

# SemiAutoRecon

SemiAutoRecon is a multi-threaded network reconnaissance tool which performs semi-automated enumeration of services. It is intended as a time-saving tool for use in CTFs and other penetration testing environments (e.g. OSCP). It is most definitely not useful in real-world engagements.

The tool works by firstly performing port scans / service detection scans (with explicit permission from the user). From those initial results, the tool will launch further enumeration scans of those services using a number of different tools (with explicit permission from the user). For example, if HTTP is found, feroxbuster will be launched (as well as many others), provided the user gives permission.

Everything in the tool is highly configurable. The default configuration performs **no automated exploitation** to keep the tool in line with OSCP exam rules. If you wish to add semi-automatic exploit tools to the configuration, you do so at your own risk. The author will not be held responsible for negative actions that result from the mis-use of this tool.

## Origin

SemiAutoRecon was inspired by three tools which the author used during the OSCP labs: [Reconnoitre](https://github.com/codingo/Reconnoitre), [ReconScan](https://github.com/RoliSoft/ReconScan), and [bscan](https://github.com/welchbj/bscan). While all three tools were useful, none of the three alone had the functionality desired. SemiAutoRecon combines the best features of the aforementioned tools while also implementing many new features to help testers with enumeration of multiple targets.

## Features

* Prompts the user before executing any command.
* Supports multiple targets in the form of IP addresses, IP ranges (CIDR notation), and resolvable hostnames. IPv6 is also supported.
* Can scan multiple targets concurrently, utilizing multiple processors if they are available.
* Advanced plugin system allowing for easy creation of new scans.
* Customizable port scanning plugins for flexibility in your initial scans.
* Customizable service scanning plugins for further enumeration.
* Suggested manual follow-up commands for when automation makes little sense.
* Ability to limit port scanning to a combination of TCP/UDP ports.
* Ability to skip port scanning phase by suppling information about services which should be open.
* Global and per-scan pattern matching which highlights and extracts important information from the noise.
* An intuitive directory structure for results gathering.
* Full logging of commands that were run, along with errors if they fail.
* A powerful config file lets you use your favorite settings every time.
* A tagging system that lets you include or exclude certain plugins.
* Global and per-target timeouts in case you only have limited time.
* Four levels of verbosity, controllable by command-line options, and during scans using Up/Down arrows.
* Colorized output for distinguishing separate pieces of information. Can be turned off for accessibility reasons.

## Installation

There are three ways to install SemiAutoRecon: pipx, pip, and manually. Before installation using any of these methods, certain requirements need to be fulfilled. If you have not refreshed your apt cache recently, run the following command so you are installing the latest available packages:

```bash
sudo apt update
```

### Python 3

SemiAutoRecon requires the usage of Python 3.7+ and pip, which can be installed on Kali Linux using the following commands:

```bash
sudo apt install python3
sudo apt install python3-pip
```

### Supporting Packages

Several commands used in SemiAutoRecon reference the SecLists project, in the directory /usr/share/seclists/. You can either manually download the SecLists project to this directory (https://github.com/danielmiessler/SecLists), or if you are using Kali Linux (**highly recommended**) you can run the following commands:

```bash
sudo apt install seclists
```

SemiAutoRecon will still run if you do not install SecLists, though several commands may fail, and some manual commands may not run either.

Additionally the following commands may need to be installed, depending on your OS:

```
curl
enum4linux
feroxbuster
gobuster
impacket-scripts
nbtscan
nikto
nmap
onesixtyone
oscanner
redis-tools
smbclient
smbmap
snmpwalk
sslscan
svwar
tnscmd10g
whatweb
wkhtmltopdf
```

On Kali Linux, you can ensure these are all installed using the following commands:

```bash
sudo apt install seclists curl enum4linux feroxbuster gobuster impacket-scripts nbtscan nikto nmap onesixtyone oscanner redis-tools smbclient smbmap snmp sslscan sipvicious tnscmd10g whatweb wkhtmltopdf
```

### Installation Method #1: pipx (Recommended)

It is recommended you use `pipx` to install SemiAutoRecon. pipx will install SemiAutoRecon in it's own virtual environment, and make it available in the global context, avoiding conflicting package dependencies and the resulting instability. First, install pipx using the following commands:


```bash
sudo apt install python3-venv
python3 -m pip install --user pipx
python3 -m pipx ensurepath
```

You will have to re-source your ~/.bashrc or ~/.zshrc file (or open a new tab) after running these commands in order to use pipx.

Install SemiAutoRecon using the following command:

```bash
pipx install git+https://github.com/Tib3rius/SemiAutoRecon.git
```

Note that if you want to run SemiAutoRecon using sudo (required for faster SYN scanning and UDP scanning), you have to use _one_ of the following examples:

```bash
sudo env "PATH=$PATH" semiautorecon [OPTIONS]
sudo $(which semiautorecon) [OPTIONS]
```

### Installation Method #2: pip

Alternatively you can use `pip` to install SemiAutoRecon using the following command:

```bash
python3 -m pip install git+https://github.com/Tib3rius/SemiAutoRecon.git
```

Note that if you want to run SemiAutoRecon using sudo (required for faster SYN scanning and UDP scanning), you will have to run the above command as the root user (or using sudo).

Similarly to `pipx`, if installed using `pip` you can run SemiAutoRecon by simply executing `semiautorecon`.

### Installation Method #3: Manually

If you'd prefer not to use `pip` or `pipx`, you can always still install and execute `semiautorecon.py` manually as a script. From within the SemiAutoRecon directory, install the dependencies:

```bash
python3 -m pip install -r requirements.txt
```

You will then be able to run the `semiautorecon.py` script:

```bash
python3 semiautorecon.py [OPTIONS] 127.0.0.1
```

## Upgrading

### pipx

Upgrading SemiAutoRecon when it has been installed with pipx is the easiest, and is why the method is recommended. Simply run the following command:

```bash
pipx upgrade semiautorecon
```

### pip

If you've installed SemiAutoRecon using pip, you will first have to uninstall SemiAutoRecon and then re-install using the same install command:

```bash
python3 -m pip uninstall semiautorecon
python3 -m pip install git+https://github.com/Tib3rius/SemiAutoRecon.git
```

### Manually

If you've installed SemiAutoRecon manually, simply change to the SemiAutoRecon directory and run the following command:

```bash
git pull
```

Assuming you did not modify any of the content in the SemiAutoRecon directory, this should pull the latest code from this GitHub repo, after which you can run SemiAutoRecon using the semiautorecon.py script as per usual.

### Plugins

A plugin update process is in the works. Until then, after upgrading, remove the ~/.config/SemiAutoRecon directory and run SemiAutoRecon with any argument to repopulate with the latest files.

If you depend on the ~/.config/SemiAutoRecon/config.toml file (i.e. you have made modifications to it) then simply remove everything in the ~/.config/SemiAutoRecon apart from the config.toml file (including the VERSION-x.x.x file).

## Usage

SemiAutoRecon uses Python 3 specific functionality and does not support Python 2.

```
usage: semiautorecon [-t TARGET_FILE] [-p PORTS] [-m MAX_SCANS] [-mp MAX_PORT_SCANS] [-c CONFIG_FILE] [-g GLOBAL_FILE] [--tags TAGS]
                 [--exclude-tags TAGS] [--port-scans PLUGINS] [--service-scans PLUGINS] [--reports PLUGINS] [--plugins-dir PLUGINS_DIR]
                 [--add-plugins-dir PLUGINS_DIR] [-l [TYPE]] [-o OUTPUT] [--single-target] [--only-scans-dir] [--no-port-dirs]
                 [--heartbeat HEARTBEAT] [--timeout TIMEOUT] [--target-timeout TARGET_TIMEOUT] [--nmap NMAP | --nmap-append NMAP_APPEND]
                 [--proxychains] [--disable-sanity-checks] [--force-services SERVICE [SERVICE ...]] [--accessible]
                 [-v] [--version] [--curl.path VALUE] [--dirbuster.tool {feroxbuster,gobuster,dirsearch,ffuf,dirb}]
                 [--dirbuster.wordlist VALUE [VALUE ...]] [--dirbuster.threads VALUE] [--dirbuster.ext VALUE]
                 [--onesixtyone.community-strings VALUE] [--global.username-wordlist VALUE] [--global.password-wordlist VALUE]
                 [--global.domain VALUE] [-h]
                 [targets ...]

Network reconnaissance tool to port scan and automatically enumerate services found on multiple targets.

positional arguments:
  targets               IP addresses (e.g. 10.0.0.1), CIDR notation (e.g. 10.0.0.1/24), or resolvable hostnames (e.g. foo.bar) to scan.

optional arguments:
  -t TARGET_FILE, --target-file TARGET_FILE
                        Read targets from file.
  -p PORTS, --ports PORTS
                        Comma separated list of ports / port ranges to scan. Specify TCP/UDP ports by prepending list with T:/U: To scan both
                        TCP/UDP, put port(s) at start or specify B: e.g. 53,T:21-25,80,U:123,B:123. Default: None
  -m MAX_SCANS, --max-scans MAX_SCANS
                        The maximum number of concurrent scans to run. Default: 50
  -mp MAX_PORT_SCANS, --max-port-scans MAX_PORT_SCANS
                        The maximum number of concurrent port scans to run. Default: 10 (approx 20% of max-scans unless specified)
  -c CONFIG_FILE, --config CONFIG_FILE
                        Location of SemiAutoRecon's config file. Default: ~/.config/SemiAutoRecon/config.toml
  -g GLOBAL_FILE, --global-file GLOBAL_FILE
                        Location of SemiAutoRecon's global file. Default: ~/.config/SemiAutoRecon/global.toml
  --tags TAGS           Tags to determine which plugins should be included. Separate tags by a plus symbol (+) to group tags together. Separate
                        groups with a comma (,) to create multiple groups. For a plugin to be included, it must have all the tags specified in
                        at least one group. Default: default
  --exclude-tags TAGS   Tags to determine which plugins should be excluded. Separate tags by a plus symbol (+) to group tags together. Separate
                        groups with a comma (,) to create multiple groups. For a plugin to be excluded, it must have all the tags specified in
                        at least one group. Default: None
  --port-scans PLUGINS  Override --tags / --exclude-tags for the listed PortScan plugins (comma separated). Default: None
  --service-scans PLUGINS
                        Override --tags / --exclude-tags for the listed ServiceScan plugins (comma separated). Default: None
  --reports PLUGINS     Override --tags / --exclude-tags for the listed Report plugins (comma separated). Default: None
  --plugins-dir PLUGINS_DIR
                        The location of the plugins directory. Default: ~/.config/SemiAutoRecon/plugins
  --add-plugins-dir PLUGINS_DIR
                        The location of an additional plugins directory to add to the main one. Default: None
  -l [TYPE], --list [TYPE]
                        List all plugins or plugins of a specific type. e.g. --list, --list port, --list service
  -o OUTPUT, --output OUTPUT
                        The output directory for results. Default: results
  --single-target       Only scan a single target. A directory named after the target will not be created. Instead, the directory structure will
                        be created within the output directory. Default: False
  --only-scans-dir      Only create the "scans" directory for results. Other directories (e.g. exploit, loot, report) will not be created.
                        Default: False
  --no-port-dirs        Don't create directories for ports (e.g. scans/tcp80, scans/udp53). Instead store all results in the "scans" directory
                        itself. Default: False
  --heartbeat HEARTBEAT
                        Specifies the heartbeat interval (in seconds) for scan status messages. Default: 60
  --timeout TIMEOUT     Specifies the maximum amount of time in minutes that SemiAutoRecon should run for. Default: None
  --target-timeout TARGET_TIMEOUT
                        Specifies the maximum amount of time in minutes that a target should be scanned for before abandoning it and moving on.
                        Default: None
  --nmap NMAP           Override the {nmap_extra} variable in scans. Default: -vv --reason -Pn -T4
  --nmap-append NMAP_APPEND
                        Append to the default {nmap_extra} variable in scans. Default:
  --proxychains         Use if you are running SemiAutoRecon via proxychains. Default: False
  --disable-sanity-checks
                        Disable sanity checks that would otherwise prevent the scans from running. Default: False
  --force-services SERVICE [SERVICE ...]
                        A space separated list of services in the following style: tcp/80/http tcp/443/https/secure
  --accessible          Attempts to make SemiAutoRecon output more accessible to screenreaders. Default: False
  -v, --verbose         Enable verbose output. Repeat for more verbosity.
  --version             Prints the SemiAutoRecon version and exits.
  -h, --help            Show this help message and exit.

plugin arguments:
  These are optional arguments for certain plugins.

  --curl.path VALUE     The path on the web server to curl. Default: /
  --dirbuster.tool {feroxbuster,gobuster,dirsearch,ffuf,dirb}
                        The tool to use for directory busting. Default: feroxbuster
  --dirbuster.wordlist VALUE [VALUE ...]
                        The wordlist(s) to use when directory busting. Separate multiple wordlists with spaces. Default:
                        ['~/.config/SemiAutoRecon/wordlists/dirbuster.txt']
  --dirbuster.threads VALUE
                        The number of threads to use when directory busting. Default: 10
  --dirbuster.ext VALUE
                        The extensions you wish to fuzz (no dot, comma separated). Default: txt,html,php,asp,aspx,jsp
  --onesixtyone.community-strings VALUE
                        The file containing a list of community strings to try. Default: /usr/share/seclists/Discovery/SNMP/common-snmp-
                        community-strings-onesixtyone.txt

global plugin arguments:
  These are optional arguments that can be used by all plugins.

  --global.username-wordlist VALUE
                        A wordlist of usernames, useful for bruteforcing. Default: /usr/share/seclists/Usernames/top-usernames-shortlist.txt
  --global.password-wordlist VALUE
                        A wordlist of passwords, useful for bruteforcing. Default: /usr/share/seclists/Passwords/darkweb2017-top100.txt
  --global.domain VALUE
                        The domain to use (if known). Used for DNS and/or Active Directory. Default: None
```

### Verbosity

SemiAutoRecon supports four levels of verbosity:

* (none) Minimal output. SemiAutoRecon will announce when scanning targets starts / ends.
* (-v) Verbose output. SemiAutoRecon will additionally announce when plugins start running, and report open ports and identified services.
* (-vv) Very verbose output. SemiAutoRecon will additionally specify the exact commands which are being run by plugins, highlight any patterns which are matched in command output, and announce when plugins end.
* (-vvv) Very, very verbose output. SemiAutoRecon will output everything. Literally every line from all commands which are currently running. When scanning multiple targets concurrently, this can lead to a ridiculous amount of output. It is not advised to use -vvv unless you absolutely need to see live output from commands.

Note: You can change the verbosity of SemiAutoRecon mid-scan by pressing the up and down arrow keys.

### Results

By default, results will be stored in the ./results directory. A new sub directory is created for every target. The structure of this sub directory is:

```
.
├── exploit/
├── loot/
├── report/
│   ├── local.txt
│   ├── notes.txt
│   ├── proof.txt
│   └── screenshots/
└── scans/
	├── _commands.log
	├── _manual_commands.txt
	├── tcp80/
	├── udp53/
	└── xml/
```

The exploit directory is intended to contain any exploit code you download / write for the target.

The loot directory is intended to contain any loot (e.g. hashes, interesting files) you find on the target.

The report directory contains some auto-generated files and directories that are useful for reporting:
* local.txt can be used to store the local.txt flag found on targets.
* notes.txt should contain a basic template where you can write notes for each service discovered.
* proof.txt can be used to store the proof.txt flag found on targets.
* The screenshots directory is intended to contain the screenshots you use to document the exploitation of the target.

The scans directory is where all results from scans performed by SemiAutoRecon will go. This includes port scans / service detection scans, as well as any service enumeration scans. It also contains two other files:
* \_commands.log contains a list of every command SemiAutoRecon ran against the target. This is useful if one of the commands fails and you want to run it again with modifications.
* \_manual_commands.txt contains any commands that are deemed "too dangerous" to run automatically, either because they are too intrusive, require modification based on human analysis, or just work better when there is a human monitoring them.

By default, directories are created for each open port (e.g. tcp80, udp53) and scan results for the services found on those ports are stored in their respective directories. You can disable this behavior using the --no-port-dirs command line option, and scan results will instead be stored in the scans directory itself.

If a scan results in an error, a file called \_errors.log will also appear in the scans directory with some details to alert the user.

If output matches a defined pattern, a file called \_patterns.log will also appear in the scans directory with details about the matched output.

The scans/xml directory stores any XML output (e.g. from Nmap scans) separately from the main scan outputs, so that the scans directory itself does not get too cluttered.
