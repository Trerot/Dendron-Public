---
id: zw4ghung18qoklidi07r553
title: Powershell-Beginners
desc: ''
updated: 1660155396059
created: 1660155381259
---
Powershell lesson for apprentices. written in english so i can easily get my powershell colleagues to help out if needed and answer questions in the Powershell Discord  

## Tools

### Visual Studio Code

Visual Studio Code vs Notepad++. VSC is just better.

- official editor for powershell. Powershell ISE deprecated
- Github integration

<https://code.visualstudio.com/>

try the keybindings. spesifically Ctrl+shift+P and alt+click
<https://code.visualstudio.com/docs/getstarted/keybindings>
<https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf>

### Github

- version control.
- sharing and collaboration.

<https://github.com/>

Install Github desktop(unless allready familiar with git)

### Powershell 7

<https://github.com/PowerShell/PowerShell>

- crossplatform
- predictive intellisense
![predictive intellisense sample](/assets/images/2022-07-04-10-43-20.png)

```powershell
# these are my predictive intellisense settings. 
# grabbed the essentials from the link below.
# https://devblogs.microsoft.com/powershell/announcing-psreadline-2-1-with-predictive-intellisense/
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -HistorySearchCursorMovesToEnd
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward
Set-PSReadLineKeyHandler -Chord "Ctrl+f" -Function ForwardWord
Set-PSReadLineKeyHandler -Chord Shift+Tab -Function MenuComplete
Set-PSReadLineKeyHandler -Chord Ctrl+b -Function BackwardWord

```

- multithreading

```powershell
# slow 1 by 1
1..10 | foreach-object { test-connection 195.88.55.$_ -count 1 -quiet}
# all 10 at the same time!
1..10 | foreach-object -ThrottleLimit 10 -Parallel { test-connection 195.88.55.$_ -count 1 -quiet}
```

- just better than powershell 5

### Task 1

after installing VSCode, Powershell 7.2+ and Githubdesktop. open powershell
type `code $profile` and paste in my predictive intellisense config.
the powershell profile loads every time you open powershell.

## Common commands

|CMD|Powershell|Powershell Alias|
|-|-|-|
|ping|Test-NetConnection / Test-Connection |tnc|
|cd|Set-Location|sl, cd, chdir|
|dir|Get-ChildItem|gci,dir,ls|
|ipconfig|Get-NetIPConfiguration|gip|
|tracert|Test-NetConnection -traceroute|tnc -tr|
|shutdown|Stop-Computer / Restart-Computer||
|type|get-content|cat, gc, type|

All powershell functions are built using verb-noun

Verb = get, set, stop, remove, test, invoke...

Noun = cat, london, chair, cleanliness, assignment, computer...

if you are creating your own functions. look at the list approved verbs <https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7.2>

with the verb-noun information we can quickly get more commands

```powershell
get-command -verb "get" -noun "loc*"
get-command -noun "location"
```

### Task 2

explore the commands in table above. ping some website

test out predictive intellisense with shift+tab. Try it with

- "`Get-NetIPConfiguration -`"
- "`Test-Connection -`"
- try out `test-connection -quiet -TargetPath "Your Favorite site"`

## Variables and data types

variable is a way to store something for easy reuse later on.

you have built inn variables like $ErrorActionPreference

```powershell
# this should display all variables
get-variable
```

and user defined ones.

```powershell
$MyGivenName = "David"
$MySurName = "Etternavn"
$MyGivenname + " " + $MySurname
$var1 = "-1"
$var2 = 2
$sum = $var1 + $var2
$sum

$var2.GetType()


[int32]$var1 + $var2

# you also have more datatypes like these or more
$var1 = $true
$var2 = 10L
$var2 = [int64]"10"
```

Enviromental variables are also quite usefull
`$env:` and shift tab will displaythem.
$env:username and $env:userprofile when writing scripts makes them easier to use for multiple users.

### Task 3

explore the different enviromental variables.
use them with for example set-location and get-childitem

## Objects and properties

```powershell
$var = "stuff written here", "more stuff written here"
# check these out
$var | get-member
get-member -inputobject $var

```

### task 4

- run the `var =` command above, try a $var.count, $var.length
- test-netconnection and test-connection your favorite website, and save them as a variable.
- try get-member and access the different properties using $var.InsertPropertyName
- try `$variable | select-object address` on both test-connection and test-netconnection variables.

## Arrays,Arraylists and hashtables

### array

creation of an empty array

```powershell
$data = @()
# check the count to see if its empty or not.
$data.count
```

creation of an array with values in one go

```powershell
$data = @('Zero','One','Two','Three')
# checking its contents.
$data.count
$data
# or multiline since its easier to read
$data = @(
    'Zero'
    'One'
    'Two'
    'Three'
)
```

comma separated lines can also create an array and work mots of the time.

```powershell
$data = 'Zero','One','Two','Three'
```

to access individual items use brackets []. remember that the first item is [0]

```powershell
#  
$data[0]
$data[3]
# negative values go from the end. so last item is [-1]
$data[-1]
# multiple also possible
$data[3,0,3]
#or range with the .. operator
$data[1..3]
# or reverse
$data[3..1]
```

updating values and adding to an array

```powershell
# updating a value
$data[1] = "fifty"
# adding to an array
$data += "four"

```

more info on arrays <https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-arrays?view=powershell-7.2>

### Arraylist

arrays are slow to manipulate when large. adding to an array doesnt append the data, it breaks down the array and stores it again.

Arraylists handles adding items quickly. I personally almost always use arraylists.

```powershell
# creating an array and adding to it.
$myarray = [System.Collections.ArrayList]::new()
[void]$myArray.Add('Value')
$somevalue ="othervalue"
[void]$myArray.Add($somevalue)
# and removing from array
$everyExistingFolder.Remove('Value')

```

`[void]` is used to supress the return code when adding values. try it without.

### Pscustomobject

```powershell
$myObject = [PSCustomObject]@{
    Name     = 'Kevin'
    Language = 'PowerShell'
    State    = 'Texas'
}
# can be accessed like a normal object
$myObject.Name
# adding new properties to an object
$myObject | Add-Member -MemberType NoteProperty -Name 'ID' -Value 'KevinMarquette'
# and looking at it
$myObject.ID
# removing a property 
$myObject.psobject.Properties.Remove("ID")
```

more info on pscustomobjects <https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-pscustomobject?view=powershell-7.2>

### Task 5

- create an array and an arraylist.
- add a psCustomObject to each, add one text string and one number(make sure its stored as an int)
- access individual items in the array and array list.
- run .gettype() on the array/arraylist and the individual items.

## piping

pipe `|` is usefull for oneliners but generaly slower than other methods, using it in the command line is fine. using it in scripts if you hit performance issues might be something to consider.

```powershell
'wuauserv' | Get-Service 
'wuauserv' | Get-Service | Stop-Service
'wuauserv' | Get-Service 
'wuauserv' | Get-Service | start-service
# you can also get the content of a text file and pipe it along

```

### task 6

- find 3 services, store them in a text file and try piping it to get-service.

```powershell
Get-Content -Path $env:USERPROFILE\Services.txt | Get-Service
```

## operators 

more about operators here
<https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators?view=powershell-7.2>
or `get-help about_operators`

there are plenty but the logical and comparison operators are the most used ones. 
### Comparison operators 
- 1 -eq 1
- 1 -ne 1
- (0..10) -contains 8
- "burgers are awesome" -like "*are*"
- "burger" -match "burger"
- 1 -gt 2
- 1 -lt 2
- 3 -ge 3

etc.

### logical operators

- 1 -eq 1 -and 1 -eq 2
- 1 -eq 1 -or 1 -eq 2
- 1 -eq 1 -xor 1 -eq 2
- -not(1 -eq 1)

-xor is exclusive or.

![exclusive or table](/assets/images/2022-07-04-16-36-32.png)

### task 7

try running the operators listed

## controll flow

### if

```powershell
if(1 -eq 1){"hurray, its true"}
# or more complex
if(test-connection -count 1 -quiet nrk.no){"NRK is online"}
```

### if not

```powershell
if (-not( 1 -eq 2)) { $true }
# or 
if (!( 1 -eq 2)) { $true }
```

### else and else if

```powershell
if (test-connection -count 1 -quiet nrk.no) { "NRK is online" }
else { "NRK is not responding" }
```

```powershell
if (test-connection -count 1 -quiet nrk.no) { "NRK is online" }
elseif (test-connection bbc.com -count 1 -quiet) { "bbc is online" }
elseif (1-eq 3){"can have many more"}
else { "NRK is not responding" }
```

### switch

switch is like an if statement. but much simpler when it comes to checking multiple conditions.

```powershell
switch (3)
{
    1 {"It is one."}
    2 {"It is two."}
    3 {"It is three."}
    4 {"It is four."}
}
# or multiple, or text.
switch (4, 2, "five")
{
    1 {"It is one." }
    2 {"It is two." }
    3 {"It is three." }
    4 {"It is four." }
    3 {"Three again."}
    "five" {"It is five."}
}
```
more about switches <https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_switch?view=powershell-7.2>


### loops

#### 

for, foreach, methods
while, do/while/until

## Creating a function

should hand out a cheat sheet for this stuff abve. quick look up on the things mentioned above so they can easily look at when doing some ez labwork.

## Modules

Some stuff from these modules as they will daily look at AD and SCCM stuff.
AD module (get-aduser, get-adgroup etc).
SCCM module to find deployed computers, who has logged on, their ip etc.
excel module

## API

Then some api stuff like this. possibly a webhook on a discord server or a teams room just to get to some easy post stuff(no tokens)

```powershell
#flat easy json file to grasp
$ip = "1.1.1.1"
Invoke-RestMethod -uri "https://ipinfo.io/$ip/json"
# this weather one has alot more depth with forecast every hour etc. so getting the weather right now requires some more 
$lat = "34.05"
$lon = "-118.24"
$weather = Invoke-RestMethod -uri "https://api.met.no/weatherapi/locationforecast/2.0/compact?&lat=$lat&lon=$lon"
$weather.properties.timeseries[0].data.instant.details
```
