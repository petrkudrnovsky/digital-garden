# XQuery & XPath Cheatsheet

## Sample Data

```xml
<cinema>
  <session movie="The Martian" length="144" rating="85">
    <ticket seat="A05" type="student"/>
    <ticket seat="A06" type="adult"/>
    <label>3D</label>
  </session>
  <session movie="Interstellar" length="169"/>
  <session movie="Arrival" length="117" rating="90">
    <ticket seat="C10" type="student"/>
    <label>3D</label>
    <label>EN</label>
  </session>
  <session movie="Blade Runner" length="163" rating="78">
    <ticket seat="B01" type="adult"/>
  </session>
</cinema>
```

---

## Path Expressions

### Axes

|Axis|Syntax|Example|
|---|---|---|
|child (default)|`child::name` or `name`|`/cinema/session`|
|attribute|`attribute::name` or `@name`|`/cinema/session/@movie`|
|self|`self::node()` or `.`|`.//ticket`|
|parent|`parent::node()` or `..`|`../session`|
|descendant|`descendant::name`|`/descendant::ticket`|
|descendant-or-self|`descendant-or-self::node()` or `//`|`//session`|
|ancestor|`ancestor::name`|`ancestor::cinema`|
|ancestor-or-self|`ancestor-or-self::name`|`ancestor-or-self::session`|
|following-sibling|`following-sibling::name`|`ticket/following-sibling::label`|
|preceding-sibling|`preceding-sibling::name`|`label/preceding-sibling::ticket`|
|following|`following::name`|`following::session`|
|preceding|`preceding::name`|`preceding::ticket`|

```xquery
(: All tickets in document :)
//ticket
/descendant::ticket
/cinema/descendant-or-self::node()/ticket

(: Parent of ticket :)
//ticket/..
//ticket/parent::node()
//ticket/parent::session

(: All labels after first ticket in each session :)
//session/ticket[1]/following-sibling::label

(: Attribute access :)
//session/@movie
//session/attribute::movie
//session/attribute::*          (: all attributes :)
```

### Node Tests

```xquery
//session/ticket                 (: elements named 'ticket' :)
//session/*                      (: all child elements :)
//session/node()                 (: all child nodes (elements + text) :)
//session/text()                 (: text nodes only :)
//ticket/@*                      (: all attributes :)

(: Using name() function for dynamic element selection :)
//session/*[name() = "ticket" or name() = "label"]
```

### Predicates

```xquery
(: Position-based :)
//session[1]                     (: first session :)
//session[last()]                (: last session :)
//session[position() = 2]        (: second session :)
//session[position() >= 2]       (: after two sessions :)

(: Existence tests :)
//session[ticket]                (: sessions with at least one ticket :)
//session[@rating]               (: sessions with rating attribute :)
//session[label]                 (: sessions with at least one label :)

(: Negative existence (NOT exists) :)
//session[not(ticket)]           (: sessions without any ticket :)
//session[not(@rating)]          (: sessions without rating :)

(: Value comparisons in predicates :)
//session[@length > 150]
//session[@rating >= 80]
//session[@movie = "Arrival"]
//ticket[@type = "student"]

(: Sequence comparison - matches ANY value in tuple :)
//ticket[@type = ("student", "adult")]   (: type is student OR adult :)
//session[.//ticket/@type = ("student", "vip")] (: has student or vip ticket :)

(: Complex negative conditions :)
//session[not(ticket[@type = "adult"])]     (: no adult tickets :)
//session[.//ticket][not(.//@type = "adult")]  (: has tickets, none adult :)

(: Using count() = 0 for negative :)
//session[count(ticket[@type = "adult"]) = 0]  (: no adult tickets :)
//session[count(.//ticket) >= 1][count(ticket[@type = "adult"]) = 0]

(: Multiple predicates (AND logic) :)
//session[@rating][ticket]                  (: has rating AND has ticket :)
(: //session[@rating > 80][@length < 150]      (: rating>80 AND length<150 :):)

(: Logical operators :)
(: //session[@rating > 80 and @length < 150]
//session[@rating > 90 or @length < 120] :)
//session[ticket and not(ticket[@type = "adult"])]

(: Nested path in predicate :)
//session[ticket/@type = "student"]         (: has student ticket :)
//session[count(ticket) >= 2]               (: at least 2 tickets :)

(: IMPORTANT: .// vs // in predicates :)
//session[.//@type = "adult"]    (: descendant of current session :)
(: //session[//@type = "adult"]  - WRONG: searches entire document! :)

(: Subquery in predicate - cross-reference :)
//session[@movie = //favorites/movie]       (: movie in favorites :)
//ticket[@type = //allowed/@type]           (: type in allowed list :)
```

---

## Comparison Expressions

### General Comparisons (existential - sequences)

```xquery
(: Returns true if ANY pair satisfies :)
(1, 2) = (2, 3)                  (: true - 2=2 :)
(1, 2) != (1, 2)                 (: true - 1!=2 :)
(1, 2) < (2, 3)                  (: true - 1<2 :)
//session/@length > 150          (: true if any length > 150 :)

(: Operators: =  !=  <  <=  >  >= :)
```

### Value Comparisons (single values only)

```xquery
(: Both operands must be singleton :)
1 eq 1                           (: true :)
1 lt 3                           (: true :)
() eq 1                          (: empty sequence () :)
(1,2) eq 1                       (: ERROR - not singleton :)

(: Operators: eq  ne  lt  le  gt  ge :)
```

### Node Comparisons

```xquery
(: Identity test :)
$node1 is $node2                 (: same node? :)

(: Document order :)
$node1 << $node2                 (: node1 before node2? :)
$node1 >> $node2                 (: node1 after node2? :)
```

### Atomization

```xquery
(: Elements atomize to their text content :)
<a>5</a> eq <b>5</b>             (: true - both atomize to "5" :)
<a>12</a> = <a><b>1</b>2</a>     (: true - both atomize to "12" :)

(: Attributes atomize to their value :)
//session/@length > 150          (: compares numeric values :)
```

---

## FLWOR Expressions

### Basic Structure

```xquery
for $variable in sequence
let $aux := expression
where condition
order by expression ascending/descending
return result
```

### For Clause

```xquery
(: Simple iteration :)
for $s in //session
return $s/@movie

(: Multiple variables (nested loops) :)
for $s in //session, $t in $s/ticket
return <pair>{$s/@movie, $t/@seat}</pair>

(: Equivalent to :)
for $s in //session
for $t in $s/ticket
return <pair>{$s/@movie, $t/@seat}</pair>

(: Positional variable :)
for $s at $pos in //session
return <session pos="{$pos}">{$s/@movie}</session>
```

### Let Clause

```xquery
(: Bind entire sequence :)
let $sessions := //session
return count($sessions)

(: Multiple bindings :)
let $total := count(//session),
    $avg := avg(//session/@rating)
return <stats total="{$total}" avg="{$avg}"/>

(: Let binds whole sequence, for iterates :)
for $s in //session
let $ticketCount := count($s/ticket)
let $firstLabel := $s/label[1]
return <info movie="{$s/@movie}" tickets="{$ticketCount}"/>
```

### Where Clause

```xquery
for $s in //session
where $s/@rating > 80
return $s/@movie

(: Complex conditions :)
for $s in //session
where $s/@rating > 80 and count($s/ticket) >= 1
return $s/@movie

(: Negative conditions :)
for $s in //session
where not($s/ticket[@type = "adult"])
return $s/@movie
```

### Order By Clause

```xquery
(: Single criterion :)
for $s in //session
order by $s/@length
return $s/@movie

(: Multiple criteria :)
for $s in //session
order by $s/@length ascending, $s/@movie descending
return $s/@movie

(: Default is ascending :)
for $s in //session
order by xs:integer($s/@length) ascending, $s/@movie descending
return $s
```

### Combined Example (Exam-Style)

```xquery
(: Sessions without adult tickets, sorted by length asc, movie desc :)
for $s in //session
let $ticketCount := count($s/ticket)
let $firstLabel := $s/label[1]
where not($s/ticket[@type = "adult"])
order by xs:integer($s/@length) ascending, $s/@movie descending
return
  <tr>
    <td>{data($s/@movie)}</td>
    <td>{if ($firstLabel) then $firstLabel/text() else <i>Standard</i>}</td>
    <td>{$ticketCount}</td>
  </tr>
```

---

## Constructors

### Direct Constructors

```xquery
(: Static element :)
<movie>Arrival</movie>

(: Embedded expressions with {} :)
<movie title="{$s/@movie}">
  {$s/ticket}
</movie>

(: Escaping braces :)
<info>Use {{}} for literal braces</info>

(: Mixed content :)
<session>
  <name>{data($s/@movie)}</name>
  {$s/ticket}
  <count>{count($s/ticket)}</count>
</session>
```

### Computed Constructors

```xquery
(: Element with dynamic name :)
element session { "content" }
element { "dynamic-name" } { "content" }
element { $varName } { $content }

(: Attribute :)
attribute movie { "Arrival" }
attribute { "dynamic-attr" } { $value }

(: Text node :)
text { "some text" }
text { $s/@movie }

(: Combined :)
element movie {
  attribute year { 2016 },
  attribute title { $s/@movie },
  text { "Description here" }
}
```

### Value Extraction in Constructors

```xquery
(: CORRECT - extracting values :)
<line code="{data($n/@number)}"/>           (: data() for attribute value :)
<line code="{$n/@number}"/>                 (: shorthand in attr position :)
<dep>{data($n/departure/time)}</dep>        (: data() for element text :)
<dep>{$n/departure/time/text()}</dep>       (: text() for element text :)
<num>{$f/line/text()}</num>                 (: text() to get content :)

(: WRONG - embeds entire nodes :)
(: <code>{$n/@number}</code>       - keeps attr node, not value :)
(: <dep>{$n/departure/time}</dep>  - keeps element node :)

(: Copy existing attribute to new element :)
<result>{$f/@date}</result>
(: Output: <result date="2019-10-18"/> :)
```

### Building Tables (Exam Pattern)

```xquery
<table>
  <tr><th>Movie</th><th>Label</th><th>Tickets</th></tr>
  {
    for $s in //session
    where not($s/ticket[@type = "adult"])
    order by xs:integer($s/@length) ascending, $s/@movie descending
    return
      <tr>
        <td>{data($s/@movie)}</td>
        <td>{
          if ($s/label[1]) 
          then $s/label[1]/text() 
          else <i>Standard</i>
        }</td>
        <td>{count($s/ticket)}</td>
      </tr>
  }
</table>
```

---

## Conditional Expressions

```xquery
(: Basic if-then-else (else is REQUIRED) :)
if ($s/label) then $s/label[1] else "none"

(: Return empty sequence :)
if ($s/@rating > 80) then $s else ()

(: Nested conditions :)
if ($s/@rating > 90) then "excellent"
else if ($s/@rating > 70) then "good"
else "average"

(: In constructors :)
<info>{
  if ($s/label[1]) 
  then $s/label[1]/text() 
  else <i>Standard</i>
}</info>

(: Complex condition :)
if (count($s/ticket) > 0 and $s/@rating) 
then <valid>{$s/@movie}</valid>
else ()
```

---

## Quantified Expressions

### Some (existential - at least one)

```xquery
(: Returns true if at least one satisfies :)
some $t in $s/ticket satisfies $t/@type = "adult"

(: In where clause :)
for $s in //session
where some $t in $s/ticket satisfies $t/@type = "student"
return $s/@movie

(: Multiple variables :)
some $x in (1,2,3), $y in (2,3,4) satisfies $x = $y
```

### Every (universal - all must satisfy)

```xquery
(: Returns true if ALL satisfy :)
every $t in $s/ticket satisfies $t/@type = "student"

(: Sessions where all tickets are student type :)
for $s in //session
where $s/ticket and every $t in $s/ticket satisfies $t/@type = "student"
return $s/@movie

(: Note: empty sequence returns true for 'every' :)
every $x in () satisfies $x > 0   (: true! :)

(: Nested every - all lines, all flights :)
for $a in distinct-values(//airline)
let $lines := //line[airline = $a]/@number
where every $l in $lines satisfies
        every $f in //flight[line = $l] satisfies $f/aircraft
return $a

(: Alternative using count() = count() :)
for $a in distinct-values(//airline)
let $lines := //line[airline = $a]/@number
where count(//flight[line = $lines]) = count(//flight[line = $lines][aircraft])
return $a
```

### Practical Patterns

```xquery
(: Has at least one adult ticket :)
some $t in $s/ticket satisfies $t/@type = "adult"

(: Has NO adult tickets (negate with not) :)
not(some $t in $s/ticket satisfies $t/@type = "adult")
(: OR equivalently :)
every $t in $s/ticket satisfies $t/@type != "adult"
(: OR simpler :)
not($s/ticket[@type = "adult"])

(: All labels are "3D" :)
$s/label and every $l in $s/label satisfies $l = "3D"
```

---

## Functions Reference

### Aggregate Functions

```xquery
count(//session)                 (: number of items :)
sum(//session/@length)           (: sum of values :)
avg(//session/@rating)           (: average :)
min(//session/@length)           (: minimum :)
max(//session/@rating)           (: maximum :)
```

### Sequence Functions

```xquery
distinct-values(//ticket/@type)          (: unique values :)
empty(//session[@rating > 100])          (: true if empty :)
exists(//session[@rating])               (: true if non-empty :)
head(//session)                          (: first item :)
tail(//session)                          (: all except first :)
reverse(//session)                       (: reverse order :)
subsequence(//session, 2, 3)             (: items 2-4 :)
```

### Position Functions

```xquery
position()                       (: current position in context :)
last()                           (: last position :)

//session[position() = 1]        (: first :)
//session[position() = last()]   (: last :)
//session[position() <= 3]       (: first three :)
//session[last() - 1]            (: second to last :)

(: IMPORTANT: last() in context vs document :)
//session/ticket[last()]         (: last ticket IN EACH session :)
(//session/ticket)[last()]       (: last ticket IN ENTIRE document :)

(: Alternative for absolute last using axes :)
//ticket[not(following::ticket)]
```

### String Functions

```xquery
string($node)                    (: convert to string :)
string-join(("a","b","c"), ", ") (: "a, b, c" :)
string-join(//session/@movie, ", ")
concat("a", "b", "c")            (: "abc" :)
string-length("hello")           (: 5 :)
substring("hello", 2, 3)         (: "ell" :)
contains("hello", "ell")         (: true :)
starts-with("hello", "he")       (: true :)
ends-with("hello", "lo")         (: true :)
upper-case("hello")              (: "HELLO" :)
lower-case("HELLO")              (: "hello" :)
normalize-space("  a  b  ")      (: "a b" :)
translate("abc", "abc", "xyz")   (: "xyz" :)
replace("hello", "l", "L")       (: "heLLo" :)
tokenize("a,b,c", ",")           (: ("a", "b", "c") :)
```

### Node/Data Functions

```xquery
data($node)                      (: atomized value :)
data($s/@movie)                  (: attribute value as string :)
name($node)                      (: element/attribute name :)
local-name($node)                (: name without namespace :)
root($node)                      (: document root :)
```

### Type Casting

```xquery
xs:integer("123")                (: string to integer :)
xs:decimal("123.45")
xs:string(123)
xs:date("2024-01-15")
xs:integer($s/@length)           (: for proper numeric sorting :)
```

### Boolean Functions

```xquery
not($condition)                  (: negation :)
true()
false()
boolean(//session)               (: true if non-empty :)
```

---

## Common Exam Patterns

### Pattern 1: Pure XPath with Subquery Predicates

```xquery
(: Complex filtering without FLWOR :)
//flight[@date = "2019-10-18"][line = //line[arrival/airport/@country = "DEU"]/@number]

(: Wrapped in count :)
count(//flight[@date = "2019-10-18"][line = //line[arrival/airport/@country = "DEU"]/@number])

(: Multiple conditions in predicates :)
//flight[@date >= "2019-10-18"][not(aircraft)]/line
```

### Pattern 2: Filter with Negative Condition

```xquery
(: Sessions without any adult ticket :)
for $s in //session
where not($s/ticket[@type = "adult"])
return $s
```

### Pattern 3: First Item or Default

```xquery
(: First label or fallback :)
if ($s/label[1]) 
then $s/label[1]/text() 
else <i>Standard</i>

(: Alternative using sequence :)
($s/label[1]/text(), "Standard")[1]
```

### Pattern 4: Multi-Key Sorting

```xquery
for $s in //session
order by xs:integer($s/@length) ascending, $s/@movie descending
return $s
```

### Pattern 5: Grouping (using distinct-values)

```xquery
(: Movies by type :)
for $type in distinct-values(//ticket/@type)
return
  <type name="{$type}">
    {
      for $s in //session[ticket/@type = $type]
      return <movie>{data($s/@movie)}</movie>
    }
  </type>
```

### Pattern 6: Complete Table Construction

```xquery
<table>
  <tr><th>Movie</th><th>Label</th><th>Tickets</th></tr>
  {
    for $s in //session
    let $label := $s/label[1]
    let $count := count($s/ticket)
    where not($s/ticket[@type = "adult"])
    order by xs:integer($s/@length) ascending, $s/@movie descending
    return
      <tr>
        <td>{data($s/@movie)}</td>
        <td>{if ($label) then $label/text() else <i>Standard</i>}</td>
        <td>{$count}</td>
      </tr>
  }
</table>
```

### Pattern 7: Cross-Reference / Join

```xquery
(: Join using predicate with subquery :)
//flight[@date = "2019-10-18"][line = //line[arrival/airport/@country = "DEU"]/@number]

(: Join using let + where :)
for $f in //flight
let $country := //line[@number = $f/line]/arrival/airport/@country
where $f/@date = "2019-10-18" and $country = "DEU"
return $f

(: Join using let to store reference values :)
let $validLines := //line[arrival/airport/@country = "DEU"]/@number
for $f in //flight[@date = "2019-10-18"]
where $f/line = $validLines
return $f

(: Avoid duplicates using document order :)
for $s1 in //session, $s2 in //session
where $s1 << $s2 and $s1/@rating = $s2/@rating
return <pair m1="{$s1/@movie}" m2="{$s2/@movie}"/>
```

### Pattern 8: Build Results Then Filter

```xquery
(: Build all results first, then filter by computed value :)
let $results :=
  for $f in //flight[line = "EK140"]
  return
    <flight date="{$f/@date}" tickets="{count($f/tickets/ticket)}">
      {string-join($f/tickets/ticket/text(), ", ")}
    </flight>
let $avgTickets := avg($results/@tickets)
return $results[@tickets >= $avgTickets]

(: Useful when filter depends on aggregate of same data :)
let $sessions :=
  for $s in //session
  return <s rating="{$s/@rating}" movie="{$s/@movie}"/>
let $avgRating := avg($sessions/@rating)
return $sessions[@rating > $avgRating]
```

### Pattern 9: Aggregation with Condition

```xquery
(: Average rating of sessions with tickets :)
avg(//session[ticket]/@rating)

(: Total tickets for high-rated sessions :)
count(//session[@rating > 80]/ticket)

(: Comparison with average :)
//session[@rating > avg(//session/@rating)]
//line[duration > avg(//line/duration)]
```

### Pattern 10: Existence Check Variations

```xquery
(: Has attribute :)
//session[@rating]

(: Doesn't have attribute :)
//session[not(@rating)]

(: Has child element :)
//session[ticket]

(: Doesn't have child element :)
//session[not(ticket)]

(: Has child with specific value :)
//session[ticket/@type = "adult"]

(: Doesn't have child with specific value :)
//session[not(ticket[@type = "adult"])]

(: Has at least n children :)
//session[count(ticket) >= 2]

(: Has exactly n children :)
//session[count(ticket) = 1]
```

---

## Full Exam Solution (Question 1)

```xquery
<table>
  <tr><th>Movie</th><th>Label</th><th>Tickets</th></tr>
  {
    for $s in //session
    let $firstLabel := $s/label[1]
    let $ticketCount := count($s/ticket)
    where not($s/ticket[@type = "adult"])
    order by xs:integer($s/@length) ascending, $s/@movie descending
    return
      <tr>
        <td>{data($s/@movie)}</td>
        <td>{
          if ($firstLabel) 
          then $firstLabel/text() 
          else <i>Standard</i>
        }</td>
        <td>{$ticketCount}</td>
      </tr>
  }
</table>
```

**Expected output for sample data:**

```xml
<table>
  <tr><th>Movie</th><th>Label</th><th>Tickets</th></tr>
  <tr><td>Arrival</td><td>3D</td><td>1</td></tr>
  <tr><td>Interstellar</td><td><i>Standard</i></td><td>0</td></tr>
</table>
```

Sessions filtered: The Martian (has adult), Blade Runner (has adult) Sessions kept: Arrival (length=117), Interstellar (length=169) Order: by length asc → Arrival first, then Interstellar