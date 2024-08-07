bash builtins - know all!
xargs -- takes output and uses as args
	**find /usr/share/icons -name 'debian*' | xargs identify**
	but you could do this with find -exec
&& -- 2nd command will be executed only if 1st succeeded
||  -- 2nd command will be executed only if 1st DOES NOT succeed
tee -- sends output to a file and to stdout
redirection (must be to a file or fd)
	command >output.txt 2>&1
		what is >
			redirects stdout to a file or fd
			same as 1>
			2> redirects stderr
		what is &
			&> redirects both stdout and stderr
			>log.txt 2>&1
		0 = stdin, 1 = stdout, 2= stderr  
		/dev/stdin, etc
	make a file descriptor
		3<>/tmp/error.txt
	here document -- use << and ending
		`<<EOF`
		example
			`cat > "${HEADER}" << EOF`
`To: ${TO}`
`Subject: "${SUBJECT}" $([ -f "${SUCCESS}" ] && echo success || echo failed )`
`EOF`
	here string -- use <<<
		**wc -c <<<"How many characters in this Here string?"**
pipes -- into another process (not a file)
	**cat /proc/cpuinfo | grep 'model name' | uniq**
backticks or $() --	replaces with output of command
echo -e include escape chars
printf "Operating system:%sUnallocated RAM:%d MB" $OS $(( $FREE / 1024**2 ))
	1st replacement is %s. 2nd is %d (digit)
	Can store pattern as var
		`MSG='Operating system:%sUnallocated RAM:%d MB'`
`printf "$MSG" $OS $(( $FREE / 1024**2 ))`

alias -- alone: print all aliases
	Almost always need quotes due to spaces
	unalias -- delete
### Variables
Variables may contain any alphanumerical characters (`a-z`,`A-Z`,`0-9`) as well as most other characters (`?`,`!`,`*`,`.`,`/`, etc.):

Don't need quotes unless using special chars or you want special interpretation

single and double quotes are not always interchangeable. Depending on what we are doing with a variable (assigning or referencing), the use of one or the other has implications and will yield different results. In the context of variable assignment **single quotes** take all the characters of the variable value **_literally_**, whereas **double quotes** allow for **variable substitution**

When using quotes with environment variables, single quotes make the expansion dynamic.
double quotes the expansion is done statically
#### Local Variables
By convention, local variables are written in lower-case letters.
readonly -- creates read-only vars
`set` outputs all of the currently assigned shell variables and functions.
### Global or Environment Variables

Global or environment variables exist for the current shell as well as for all subsequent processes spawned from it. By convention, environment variables are written in uppercase letters:

	export - add a var -- converts a local variable to a global/environment variable
	set -- alone gives full list of all defined vars
	unset -	remove
	export -n   turns global into local
	export -- alone gives list of defined global vars
	env or printenv give list of env vars

`set` outputs all of the currently assigned shell variables and functions.
use shopt to change options locally in the session or script

declare -a -- declare an array
	or declare implicitly
		`SIZES=( 1048576 1073741824 )`
		`SIZES[0]=1048576`
		
#### Special builtin env variables
IFS: delimiting characters splitting items in the list are defined by the `IFS` environment variable, which are the characters _space_, _tab_ and _newline_ by default.
`$?` result of the last command run. A value of `0` means success
`$$`  Shell PID
`$!`  PID of last background job
$0  name of script or shell
$1-$9  arguments
$#  number or arguments
$@, $*  array of all arguments
$_   last argument or name of script

#### Arithmetic Expressions
expr
`SUM=expr $VAL1 + $VAL2`
or
`SUM=$(( $VAL1 + $VAL2 ))`

### Functions	
function hello() {
	 echo "Hello world!"
}

Can define functions without the keyword
**greet() { greeting="Hello world!"; echo $greeting; }**

To make the function available to the current shell, we must source the file which includes it.
We will invoke it by typing its name into the terminal.

### Conditional Expressions
See also [[tools]]
These are test expressions with `test` being implied by []
    `if [ -f "$HOME/.bashrc" ]; then`
	`. "$HOME/.bashrc"`
    `fi`
note that <,>, =, != are string operators. for num comparisonsm use -eq, -ne, -lt
! is not ,-a is and, -o is or
case -- note the) and ;;
`case "$DISTRO" in`
	`debian | ubuntu | mint)`
    `echo -n "the DEB"`
  `;;`
	`centos | fedora | opensuse )`
    `echo -n "the RPM"`
  `;;`
	`*)`
    `echo -n "an unknown"`
  `;;`
`esac`
### Loop Constructs
The `for` construct walks through a given list of items — usually a list of words or any other space-separated text segments — executing the same set of commands on each one of those items. Before each iteration, the `for` instruction assigns the current item to a variable, which can then be used by the enclosed commands. 
delimiting characters splitting items in the list are defined by the `IFS` environment variable, which are the characters _space_, _tab_ and _newline_ by default.
The process is repeated until there are no more items left. The syntax of the `for` construct is:

for VARNAME in LIST
do
	COMMANDS
done

An example:
`#!/bin/bash`

`for NUM in 1 1 2 3 5 8 13`
`do`
	`echo -n "$NUM is "`
	`if [ $(( $NUM % 2 )) -ne 0 ]`
	`then`
		`echo "odd."`
	`else`
		`echo "even."`
  `fi`
`done`

for also supports C-like
`for (( IDX = 0; IDX < ${#SEQ[*]}; IDX++ ))`
`do`

the `until` construct executes a command sequence until a test command — like the `test` command itself — terminates with status 0 (success).
`SEQ=( 1 1 2 3 5 8 13 )`
`IDX=0`
`until [ $IDX -eq ${#SEQ[*]} ]`
`do`

`while` keeps repeating the set of commands if the test command terminates with status 0 (success).

##### Example
`FILE=~/.sync.list`
`FROM=$1`
`TO=$2`
`if [ ! -d "$FROM" -o ! -d "$TO" ]`
`then`
  `echo Usage:`
  `echo "$0 <SOURCEDIR> <DESTDIR>"`
  `exit 1`
`fi`
`mapfile -t LIST < $FILE`
`for (( IDX = 0; IDX < ${#LIST[*]}; IDX++ ))`
`do`
	`echo -e "$FROM/${LIST[$IDX]} \u2192 $TO/${LIST[$IDX]}";`
	`rsync -qa --delete "$FROM/${LIST[$IDX]}" "$TO";`
`done`