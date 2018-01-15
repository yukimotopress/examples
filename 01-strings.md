---
title: Strings
---


## Introduction    <!-- @@PLEAC@@_1.0 -->

``` ruby
string = '\n'                     # two characters, \ and an n
string = 'Jon \'Maddog\' Orwant'  # literal single quotes

string = "\n"                     # a "newline" character
string = "Jon \"Maddog\" Orwant"  # literal double quotes

string = %q/Jon 'Maddog' Orwant/  # literal single quotes

string = %q[Jon 'Maddog' Orwant]  # literal single quotes
string = %q{Jon 'Maddog' Orwant}  # literal single quotes
string = %q(Jon 'Maddog' Orwant)  # literal single quotes
string = %q<Jon 'Maddog' Orwant>  # literal single quotes

a = <<"EOF"
This is a multiline here document
terminated by EOF on a line by itself
EOF
```

## Accessing Substrings   <!-- @@PLEAC@@_1.1 -->

``` ruby
value = string[offset,count]
value = string[offset..-1]

string[offset,count] = newstring
string[offset..-1]   = newtail

# in Ruby we can also specify intervals by their two offsets
value = string[offset..offs2]
string[offset..offs2] = newstring

leading, s1, s2, trailing = data.unpack("A5 x3 A8 A8 A*")

fivers = string.unpack("A5" * (string.length/5))

chars = string.unpack("A1" * string.length)

string = "This is what you have"
#        +012345678901234567890  Indexing forwards  (left to right)
#         109876543210987654321- Indexing backwards (right to left)
#          note that 0 means 10 or 20, etc. above

first  = string[0, 1]       # "T"
start  = string[5, 2]       # "is"
rest   = string[13..-1]     # "you have"
last   = string[-1, 1]      # "e"
end_   = string[-4..-1]     # "have"
piece  = string[-8, 3]      # "you"

string[5, 2] = "wasn't"     # change "is" to "wasn't"
string[-12..-1] = "ondrous" # "This wasn't wondrous"
string[0, 1] = ""           # delete first character
string[-10..-1]  = ""       # delete last 10 characters

if string[-10..-1] =~ /pattern/
    puts "Pattern matches in last 10 characters"
end

string[0, 5].gsub!(/is/, 'at')

a = "make a hat"
a[0, 1], a[-1, 1] = a[-1, 1], a[0, 1]

a = "To be or not to be"
b = a.unpack("x6 A6")

b, c = a.unpack("x6 A2 X5 A2")
puts "#{b}\n#{c}\n"

def cut2fmt(*args)
    template = ''
    lastpos  = 1
    for place in args
        template += "A" + (place - lastpos).to_s + " "
        lastpos   = place
    end
    template += "A*"
    return template
end

fmt = cut2fmt(8, 14, 20, 26, 30)
```

## Establishing a Default Value  <!-- @@PLEAC@@_1.2 -->

``` ruby
# careful! "b is true" doesn't mean "b != 0" (0 is true in Ruby)
# thus no problem of "defined" later since only nil is false
# the following sets to `c' if `b' is nil or false
a = b || c

# if you need Perl's behaviour (setting to `c' if `b' is 0) the most
# effective way is to use Numeric#nonzero? (thanks to Dave Thomas!)
a = b.nonzero? || c

# you will still want to use defined? in order to test
# for scope existence of a given object
a = defined?(b) ? b : c

dir = ARGV.shift || "/tmp"
```

## Exchanging Values Without Using Temporary Variables  <!-- @@PLEAC@@_1.3 -->

``` ruby
v1, v2 = v2, v1

alpha, beta, production = %w(January March August)
alpha, beta, production = beta, production, alpha
```

## Converting Between ASCII Characters and Values  <!-- @@PLEAC@@_1.4 -->

``` ruby
num = char[0]
char = num.chr

# Ruby also supports having a char from character constant
num = ?r

char = sprintf("%c", num)
printf("Number %d is character %c\n", num, num)

ascii = string.unpack("C*")
string = ascii.pack("C*")

hal = "HAL"
ascii = hal.unpack("C*")
# We can't use Array#each since we can't mutate a Fixnum
ascii.collect! { |i|
    i + 1                         # add one to each ASCII value
}                
ibm = ascii.pack("C*")
puts ibm
```

## Processing a String One Character at a Time  <!-- @@PLEAC@@_1.5 -->

``` ruby
array = string.split('')

array = string.unpack("C*")

string.scan(/./) { |b|
    # do something with b
}

string = "an apple a day"
print "unique chars are: ", string.split('').uniq.sort, "\n"

sum = 0
for ascval in string.unpack("C*") # or use Array#each for a pure OO style :)
    sum += ascval
end
puts "sum is #{sum & 0xffffffff}" # since Ruby will go Bignum if necessary
```

## Program: slowcat

``` ruby
#!/usr/bin/ruby
#
# slowcat - emulate a   s l o w   line printer
# usage: slowcat [-DELAY] [files ...]
# the following line with "&&" works because all strings are true

delay = ARGV[0] =~ /^-([.\d]+)/ ? ARGV.shift && $1.to_i : 1
$stdout.sync = true
while gets
    for b in split('')
        print b
        sleep(0.005 * delay)
    end
end
```

## Reversing a String by Word or Character  <!-- @@PLEAC@@_1.6 -->

``` ruby
revbytes = string.reverse

revwords = string.split(" ").reverse.join(" ")

revwords = string.split(/(\s+)/).reverse.join

# using the fact that IO is Enumerable, you can directly "select" it
long_palindromes = File.open("/usr/share/dict/words").
    select { |w| w.chomp!; w.reverse == w && w.length > 5 }
```

## Expanding and Compressing Tabs  <!-- @@PLEAC@@_1.7 -->

``` ruby
while string.sub!("\t+") { ' ' * ($&.length * 8 - $`.length % 8) }
end
```

## Expanding Variables in User Input  <!-- @@PLEAC@@_1.8 -->

``` ruby
'You owe #{debt} to me'.gsub(/\#{(\w+)}/) { eval($1) }

rows, cols = 24, 80
text = %q(I am #{rows} high and #{cols} long)
text.gsub!(/\#{(\w+)}/) { eval("#{$1}") }
puts text

'I am 17 years old'.gsub(/\d+/) { 2 * $&.to_i }
```

## Controlling Case  <!-- @@PLEAC@@_1.9 -->

``` ruby
e = "bo peep".upcase
e.downcase!
e.capitalize!

"thIS is a loNG liNE".gsub!(/\w+/) { $&.capitalize }
```

## Interpolating Functions and Expressions Within Strings <!-- @@PLEAC@@_1.10 -->

``` ruby
"I have #{n+1} guanacos."
print "I have ", n+1, " guanacos."
```


## Indenting Here Documents <!-- @@PLEAC@@_1.11 -->

``` ruby
var = <<'EOF'.gsub(/^\s+/, '')
    your text
    goes here
EOF
```

## Reformatting Paragraphs  <!-- @@PLEAC@@_1.12 -->

``` ruby
string = "Folding and splicing is the work of an editor,\n"+
    "not a mere collection of silicon\n"+
    "and\n"+
    "mobile electrons!"

def wrap(str, max_size)
    all = []
    line = ''
    for l in str.split
        if (line+l).length >= max_size
            all.push(line)
            line = ''
        end
        line += line == '' ? l : ' ' + l
    end
    all.push(line).join("\n")
end

print wrap(string, 20)
#=> Folding and
#=> splicing is the
#=> work of an editor,
#=> not a mere
#=> collection of
#=> silicon and mobile
#=> electrons!
```

## Escaping Characters  <!-- @@PLEAC@@_1.13 -->

``` ruby
string = %q(Mom said, "Don't do that.")
string.gsub(/['"]/) { '\\'+$& }
string.gsub(/['"]/, '\&\&')
string.gsub(/[^A-Z]/) { '\\'+$& }
"is a test!".gsub(/\W/) { '\\'+$& }  # no function like quotemeta?
```

## Trimming Blanks from the Ends of a String  <!-- @@PLEAC@@_1.14 -->

``` ruby
string.strip!
```

## Parsing Comma-Separated Data <!-- @@PLEAC@@_1.15 -->

``` ruby
def parse_csv(text)
    new = text.scan(/"([^\"\\]*(?:\\.[^\"\\]*)*)",?|([^,]+),?|,/)
    new << nil if text[-1] == ?,
    new.flatten.compact
end  

line = %q<XYZZY,"","O'Reilly, Inc","Wall, Larry","a \"glug\" bit,",5,"Error, Core Dumped">
fields = parse_csv(line)
fields.each_with_index { |v,i|
    print "#{i} : #{v}\n";
}
```

## Soundex Matching  <!--  @@PLEAC@@_1.16 -->

``` ruby
# Use the soundex.rb Library from Michael Neumann.
# http://www.s-direktnet.de/homepages/neumann/rb_prgs/Soundex.rb
require 'Soundex'

code = Text::Soundex.soundex(string)
codes = Text::Soundex.soundex(array)

# substitution function for getpwent():
# returns an array of user entries,
# each entry contains the username and the full name
def login_names
    result = []
    File.open("/etc/passwd") { |file|
        file.each_line { |line|
            next if line.match(/^#/)
            cols = line.split(":")
            result.push([cols[0], cols[4]])
        }
    }
    result
end

puts "Lookup user: "
user = STDIN.gets
user.chomp!
exit unless user
name_code = Text::Soundex.soundex(user)

splitter = Regexp.new('(\w+)[^,]*\b(\w+)')
for username, fullname in login_names do
    firstname, lastname = splitter.match(fullname)[1,2]
    if name_code == Text::Soundex.soundex(username)
        || name_code == Text::Soundex.soundex(firstname)
        || name_code == Text::Soundex.soundex(lastname)
    then
        puts "#{username}: #{firstname} #{lastname}"
    end
end
```

## Program: fixstyle  <!-- @@PLEAC@@_1.17 -->

``` ruby
# fixstyle - switch first set of <DATA> strings to second set
#   usage: $0 [-v] [files ...]
#   -v = verbose  prints "a->b in file line N" for every change.
# If no filenames are given, than the script runs as simple filter,
# else the files are edited in place, and a safety copy
# with the exentions ".orig" is created

# Regular expressions are objects in ruby, we have control
# when we compile them, so we don't need the eval hack
# from the perl solution.
# The changer class encapsulates the regular expression
# and its substitution string
class Changer

    def initialize(regex, subst)
        @regex = Regexp.compile(regex)
        @subst = subst
    end

    def change(string)
        changed = string.gsub!(@regex, @subst)
        if changed && $verbose
            $stderr.puts("#{@regex.source} changed to #{@subst} at #{$FILENAME} line #{$.}")
        end
    end

end

# get the lines from the Data section at the end of the file
# and put them in the list of Changer objects
def get_subs_from_end()
    changelist = []
    DATA.each do |line|
        line.chomp!
        (pat, subst) = line.split(/\s*=>\s*/)
        if pat && subst
            changelist.push(Changer.new(pat, subst))
        end
    end
    changelist
end

changelist = get_subs_from_end()

if ARGV && ARGV[0] == "-v"
    ARGV.shift
    $verbose = true
else
    $verbose = false
end

if ARGV.length > 0
    $-i = ".orig"     # enables in-place edit mode
elsif test(?e, $stdin)
    $stderr.puts("#{$0}:Reading from stdin")
end

while line = gets()
    for changer in changelist
        changer.change(line)
    end
    puts(line)
end

__END__

# analysed  => analyzed
# build-in  => builtin
# chastized => chatis
# commandline     => command-line
# de-allocate     => deallocate
# dropin          => drop-in
# hardcode        => hard-code
# meta-data       => metadata
# multicharacter  => multi-character
# multiway        => multi-way
# non-empty       => nonempty
# non-profit      => nonprofit
# non-trappable   => nontrappable
# pre-define      => predefine
# preextend       => pre-extend
# re-compiling    => recompiling
# reenter         => re-enter
# turnkey         => turn-key
```

## Program: psgrep  <!-- @@PLEAC@@_1.18 -->

``` ruby
#!/usr/bin/ruby -w
#
# psgrep - print selected lines of ps output by
#          compiling user queries into code
#-----------------------------
#% psgrep '~/sh\b/'
#-----------------------------
#% psgrep 'command =~ /sh$/'
#-----------------------------
#% psgrep 'uid < 10'
#-----------------------------
#% psgrep 'command =~ /^-/' 'tty ne "?"'
#-----------------------------
#% psgrep 'tty =~ /^[p-t]/'
#-----------------------------
#% psgrep 'uid && tty eq "?"'
#-----------------------------
#% psgrep 'size > 10 * 2**10' 'uid != 0'
#-----------------------------

class PS
    PS::Names = %w-flags uid pid ppid pri nice size
                   rss wchan stat tty time command-
    PS::Names.each {|sym| attr_accessor sym}
    attr_accessor :line
    def set_fields
        fields = line.split(" ",13)
        PS::Names.each_with_index do |sym,i|
            eval "self.#{sym} = " +
                ((0..7).include?(i) ? fields[i] : "'#{fields[i]}'")
        end
    end
end

raise <<USAGE unless ARGV.size > 0
    usage: $0 criterion ...
     #{PS::Names.join(" ")}
    All criteria must be met for a line to be printed
USAGE

PS.class_eval <<CRITERIA
    def is_desirable
        $_ = line
        #{ARGV.join(" and ")}
     end
CRITERIA

ps = PS.new
File.popen("ps wwaxl") do |f|
    puts f.gets
    f.each do |ps.line|
        ps.set_fields
        print ps.line if ps.is_desirable
    end
end
```
