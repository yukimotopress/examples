---
title: Numbers
---

## Checking Whether a String Is a Valid Number <!-- @@PLEAC@@_2.1 -->

``` ruby
# Matz tells that you can use Integer() for strict checked conversion.
Integer("abc")
#=> `Integer': invalid value for Integer: "abc" (ArgumentError)
Integer("567")
#=> 567

# You may use Float() for floating point stuff
Integer("56.7")
#=> `Integer': invalid value for Integer: "56.7" (ArgumentError)
Float("56.7")
#=> 56.7

# You may also use a regexp for that
if string =~ /^[+-]?\d+$/
    p 'is an integer'
else
    p 'is not'
end

if string =~ /^-?(?:\d+(?:\.\d*)?|\.\d+)$/
    p 'is a decimal number'
else
    p 'is not'
end
```

## Comparing Floating-Point Numbers <!-- @@PLEAC@@_2.2 -->

``` ruby
# equal(num1, num2, accuracy) : returns true if num1 and num2 are
#   equal to accuracy number of decimal places
def equal(i, j, a)
    sprintf("%.#{a}g", i) == sprintf("%.#{a}g", j)
end

wage = 536                        # $5.36/hour
week = 40 * wage                  # $214.40
printf("One week's wage is: \$%.2f\n", week/100.0)
```

## Rounding Floating-Point Numbers  <!-- @@PLEAC@@_2.3 -->

``` ruby
num.round                         # rounds to integer

a = 0.255
b = sprintf("%.2f", a)
print  "Unrounded: #{a}\nRounded: #{b}\n"
printf "Unrounded: #{a}\nRounded: %.2f\n", a

print "number\tint\tfloor\tceil\n"
a = [ 3.3 , 3.5 , 3.7, -3.3 ]
for n in a
    printf("% .1f\t% .1f\t% .1f\t% .1f\n",  # at least I don't fake my output :)
           n, n.to_i, n.floor, n.ceil)
end
```

## Converting Between Binary and Decimal <!-- @@PLEAC@@_2.4 -->

``` ruby
def dec2bin(n)
    [n].pack("N").unpack("B32")[0].sub(/^0+(?=\d)/, '')
end

def bin2dec(n)
    [("0"*32+n.to_s)[-32..-1]].pack("B32").unpack("N")[0]
end
```

## Operating on a Series of Integers <!-- @@PLEAC@@_2.5 -->

``` ruby
for i in x .. y
    # i is set to every integer from x to y, inclusive
end

x.step(y,7) { |i|
    # i is set to every integer from x to y, stepsize = 7
}

print "Infancy is: "
(0..2).each { |i|
    print i, " "
}
print "\n"
```

## Working with Roman Numerals  <!-- @@PLEAC@@_2.6 -->

``` ruby
# We can add conversion methods to the Integer class,
# this makes a roman number just a representation for normal numbers.
class Integer

    @@romanlist = [["M", 1000],
                   ["CM", 900],
                   ["D",  500],
                   ["CD", 400],
                   ["C",  100],
                   ["XC",  90],
                   ["L",   50],
                   ["XL",  40],
                   ["X",   10],
                   ["IX",   9],
                   ["V",    5],
                   ["IV",   4],
                   ["I",    1]]

    def to_roman
        remains = self
        roman = ""
        for sym, num in @@romanlist
            while remains >= num
                remains -= num
                roman << sym
            end
        end
        roman
    end

    def Integer.from_roman(roman)
        ustr = roman.upcase
        sum = 0
        for entry in @@romanlist
            sym, num = entry[0], entry[1]
            while sym == ustr[0, sym.length]
                sum += num
                ustr.slice!(0, sym.length)
            end
        end
        sum
    end

end


roman_fifteen = 15.to_roman
puts "Roman for fifteen is #{roman_fifteen}"
i = Integer.from_roman(roman_fifteen)
puts "Converted back, #{roman_fifteen} is #{i}"

# check
for i in (1..3900)
    r = i.to_roman
    j = Integer.from_roman(r)
    if i != j
        puts "error: #{i} : #{r} - #{j}"
    end
end
```

## Generating Random Numbers <!-- @@PLEAC@@_2.7 -->

``` ruby
random = rand(y-x+1)+x

chars = ["A".."Z","a".."z","0".."9"].collect { |r| r.to_a }.join + %q(!@$%^&*)
password = (1..8).collect { chars[rand(chars.size)] }.pack("C*")
```


## Generating Different Random Numbers <!-- @@PLEAC@@_2.8 -->

``` ruby
srand        # uses a combination of the time, the process id, and a sequence number
srand(val)   # for repeatable behaviour
```

## Making Numbers Even More Random  <!-- @@PLEAC@@_2.9 -->

``` ruby
# from the randomr lib:
# http://raa.ruby-lang.org/project/randomr/
# ----> http://raa.ruby-lang.org/project/randomr/

require 'random/mersenne_twister'
mers = Random::MersenneTwister.new 123456789
puts mers.rand(0)    # 0.550321932544541
puts mers.rand(10)   # 2

# using online sources of random data via the realrand package:
# http://raa.ruby-lang.org/project/realrand/
# **Note**
# The following online services are used in this package:
#   http://www.random.org - source: atmospheric noise
#   http://www.fourmilab.ch/hotbits - source: radioactive decay timings
#   http://random.hd.org - source: entropy from local and network noise
# Please visit the sites and respect the rules of each service.

require 'random/online'

generator1 = Random::RandomOrg.new
puts generator1.randbyte(5).join(",")
puts generator1.randnum(10, 1, 6).join(",")  # Roll dice 10 times.

generator2 = Random::FourmiLab.new
puts generator2.randbyte(5).join(",")
# randnum is not supported.

generator3 = Random::EntropyPool.new
puts generator3.randbyte(5).join(",")
# randnum is not supported.
```


## Generating Biased Random Numbers <!-- @@PLEAC@@_2.10 -->

``` ruby
def gaussian_rand
    begin
        u1 = 2 * rand() - 1
        u2 = 2 * rand() - 1
        w = u1*u1 + u2*u2
    end while (w >= 1)
    w = Math.sqrt((-2*Math.log(w))/w)
    [ u2*w, u1*w ]
end

mean = 25
sdev = 2
salary = gaussian_rand[0] * sdev + mean
printf("You have been hired at \$%.2f\n", salary)
```

## Doing Trigonometry in Degrees, not Radians <!-- @@PLEAC@@_2.11 -->

``` ruby
def deg2rad(d)
    (d/180.0)*Math::PI
end

def rad2deg(r)
    (r/Math::PI)*180
end
```

## Calculating More Trigonometric Functions <!-- @@PLEAC@@_2.12 -->

``` ruby
sin_val = Math.sin(angle)
cos_val = Math.cos(angle)
tan_val = Math.tan(angle)

# AFAIK Ruby's Math module doesn't provide acos/asin
# While we're at it, let's also define missing hyperbolic functions
module Math
    def Math.asin(x)
        atan2(x, sqrt(1 - x**2))
    end
    def Math.acos(x)
        atan2(sqrt(1 - x**2), x)
    end
    def Math.atan(x)
        atan2(x, 1)
    end
    def Math.sinh(x)
        (exp(x) - exp(-x)) / 2
    end
    def Math.cosh(x)
        (exp(x) + exp(-x)) / 2
    end
    def Math.tanh(x)
        sinh(x) / cosh(x)
    end
end

# The support for Complex numbers is not built-in
y = Math.acos(3.7)
#=> in `sqrt': square root for negative number (ArgumentError)

# There is an implementation of Complex numbers in 'complex.rb' in current
# Ruby distro, but it doesn't support atan2 with complex args, so it doesn't
# solve this problem.
```


## Taking Logarithms   <!-- @@PLEAC@@_2.13 -->

``` ruby
log_e = Math.log(val)
log_10 = Math.log10(val)

def log_base(base, val)
    Math.log(val)/Math.log(base)
end

answer = log_base(10, 10_000)
puts "log10(10,000) = #{answer}"
```


## Multiplying Matrices  <!-- @@PLEAC@@_2.14 -->

``` ruby
require 'matrix.rb'

a = Matrix[[3, 2, 3], [5, 9, 8]]
b = Matrix[[4, 7], [9, 3], [8, 1]]
c = a * b

a.row_size
a.column_size

c.det
a.transpose
```


## Using Complex Numbers <!-- @@PLEAC@@_2.15 -->

``` ruby
require 'complex.rb'
require 'rational.rb'

a = Complex(3, 5)              # 3 + 5i
b = Complex(2, -2)             # 2 - 2i
puts "c = #{a*b}"

c = a * b
d = 3 + 4*Complex::I

printf "sqrt(#{d}) = %s\n", Math.sqrt(d)
```

## Converting Between Octal and Hexadecimal  <!-- @@PLEAC@@_2.16 -->

``` ruby
number = hexadecimal.hex
number = octal.oct

print "Gimme a number in decimal, octal, or hex: "
num = gets.chomp
exit unless defined?(num)
num = num.oct if num =~ /^0/  # does both oct and hex  
printf "%d %x %o\n", num, num, num

print "Enter file permission in octal: "
permissions = gets.chomp
raise "Exiting ...\n" unless defined?(permissions)
puts "The decimal value is #{permissions.oct}"
```

## Putting Commas in Numbers   <!-- @@PLEAC@@_2.17 -->

``` ruby
def commify(n)
    n.to_s =~ /([^\.]*)(\..*)?/
    int, dec = $1.reverse, $2 ? $2 : ""
    while int.gsub!(/(,|\.|^)(\d{3})(\d)/, '\1\2,\3')
    end
    int.reverse + dec
end
```

## Printing Correct Plurals  <!-- @@PLEAC@@_2.18 -->

``` ruby
printf "It took %d hour%s\n", time, time == 1 ? "" : "s"

# dunno if an equivalent to Lingua::EN::Inflect exists...
```


## Program: Calculating Prime Factors <!-- @@PLEAC@@_2.19 -->

``` ruby
#!/usr/bin/ruby
#
# bigfact - calculating prime factors

def factorize(orig)
    factors = {}
    factors.default = 0     # return 0 instead nil if key not found in hash
    n = orig
    i = 2
    sqi = 4                 # square of i
    while sqi <= n do
        while n.modulo(i) == 0 do
            n /= i
            factors[i] += 1
            # puts "Found factor #{i}"
        end
        # we take advantage of the fact that (i +1)**2 = i**2 + 2*i +1
        sqi += 2 * i + 1
        i += 1
    end

    if (n != 1) && (n != orig)
        factors[n] += 1
    end
    factors
end

def printfactorhash(orig, factorcount)
    print format("%-10d ", orig)
    if factorcount.length == 0
        print "PRIME"
    else
        # sorts after number, because the hash keys are numbers
        factorcount.sort.each { |factor,exponent|
            print factor
            if exponent > 1
                print "**", exponent
            end
            print " "
        }
    end
    puts
end

for arg in ARGV
    n = arg.to_i
    mfactors = factorize(n)
    printfactorhash(n, mfactors)
end
```
