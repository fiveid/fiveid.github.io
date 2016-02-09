---
layout: post
title: Playing Around with Enums, Classes and Protocols in Swift
---

An enum (short for enumeration) is a predefined collection of related values. Think of it like a dropdown menu for use in your code. What this means and why this is useful was the original purpose of this random exercise. If you want to follow along, simply open up a Playground and replicate the code as I ramble along.

_Note: I don't go into much detail about the language syntax. This isn't a tutorial, as such. More of a fun example of what can be done. I may publish a companion post that explains some parts of the code more fully, but that's not the aim here._

My starting point was deciding what a good real-world example of an enum would be. The days of the week seemed an obvious choice. After all, there are seven days in the week and you can be certain that's not going to change any time soon.

First, let's define an enum to represent the days and assign today to a variable.

~~~swift
enum Day: Int {
    case Monday = 0, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday
}

var today = Day.Thursday
~~~

That gives us the day of the week stored in a `var`. But couldn't I have just written something like this?

~~~swift
var today = "Thursday";
~~~

Well, that's no fun. And there are a few reasons why Enums are recommended. For one, they provide a degree of certainty. You (or someone else working on your code) can not accidentally assign an incorrect value. For example, `Day.Tutherday` will cause an error and prevent the program from being compiled, which is good.

Enums also offer clarity. By using `Day.Monday` we can clearly see what's being defined. This is something we don't get if we, for example, stored all our values in an array and selected them by index.

Finally, Enums in Swift are Types. This means that, in our example, we can easily check whether the value is in fact a `Day`. This also means we can add all sorts of funky functionality.

We'll edit our Enum to add a function that will allow us to print what day of the week it is.

~~~swift
enum Day: Int {
    case Monday = 0, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday

    func checkDay() -> String {
        switch self.rawValue {
        case 0:
            return "The day is Monday"
        case 1:
            return "The day is Tuesday"
        case 2:
            return "The day is Wednesday"
        case 3:
            return "The day is Thursday"
        case 4:
            return "The day is Friday"
        case 5:
            return "The day is Saturday"
        case 6:
            return "The day is Sunday"
        default:
            return "There's only 7 days in a week"
        }
    }
}

var today = Day.Thursday
today.checkDay() // "The day is Thursday"
~~~

Amazing! Well, maybe not. That's pretty useless at the moment. We know it's a Thursday because we've just defined it as a Thursday. Duh.

But it's a cool feature. If you have some programming experience, you'll be familiar with using standard functions on built-in types (think of calling `toUpperCase()` on a `String` variable). In Swift, we're encouraged to build our own. We have the power.

We can do something a bit more useful and add a function that will fast forward us 24 hours in time and change the value to the next day.

~~~swift
enum Day: Int {
    case Monday = 0, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday

    func checkDay() -> String {
        // ...
    }

    mutating func next() -> Void {
        if let nextDay = Day(rawValue: (self.rawValue + 1)) {
            self = nextDay
        } else {
            self = Day.Monday
        }
    }
}

var today = Day.Thursday
today.next()
today.checkDay() // "The day is Friday"
~~~

_Note: if you're following along in a Playground, you might have noticed that our `checkDay()` function no longer seems so useless!_

That's handy. Now let's also create a function that takes us back in time 24 hours to the previous day.

~~~swift
enum Day: Int {
    case Monday = 0, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday

    func checkDay() -> String {
        // ...
    }

    mutating func next() -> Void {
        // ...
    }
    mutating func prev() -> Void {
        if let prevDay = Day(rawValue: (self.rawValue - 1)) {
            self = prevDay
        } else {
            self = Day.Sunday
        }
    }
}

var today = Day.Thursday
today.prev()
today.checkDay() // "The day is Wednesday"
~~~

Cool. We've now got an enum for the days of the week and the ability to cycle backwards and forwards through time.

What should we use that for?

Obviously, we should use it to check what Craig David is up to on any given day.

Let's create a CraigDavid class. Within, we'll add a function called `onThisDay()` that takes our enum (`Day`) and returns a `String` to describe what he's doing on that particular day.

The easiest way to check the value of an enum is to use a `switch` statement.

~~~swift
class CraigDavid {
    func onThisDay(day: Day) -> String {
        switch day {
        case .Monday:
            return "Meeting this girl"
        case .Tuesday:
            return "Taking her for a drink"
        case .Wednesday:
            return "Making love"
        case .Thursday:
            return "Making love"
        case .Friday:
            return "Making love"
        case .Saturday:
            return "Making love"
        case .Sunday:
            return "Chilling"
        }
    }
}

let craig = CraigDavid()
craig.onThisDay(today) // "Making love"

today.next()
craig.onThisDay(today) // "Making love"
~~~

It works! But to find out what Craig David is doing on every day of the week, we'd have to manually change our `Day` and get repetitive with our code. Not good enough. We should create a function containing a loop that'll do that for us.

This function requires a `Day` (representing the start day), a person (which is `CraigDavid`) and then cycles through the 7 days of the week.

~~~swift
func dailyActivity(var day: Day, person: CraigDavid) {
    for _ in 1...7 {
        person.onThisDay(day)
        day.next()
    }
}

dailyActivity(today, craig)
// Making love / Making love / Making love / Chilling / Meeting this girl / Taking her for a drink / Making love
~~~

Sorted. Now we know what Craig David's schedule holds, let's check in on another artist's daily activity. We should create a class for the indy rock band Hard-Fi.

We'll need to create another class function for `onThisDay()`, although this time the `switch` statement will be a little different.

~~~swift
class HardFi {
    func onThisDay(day: Day) -> String {
        switch day.rawValue {
        case 4, 5:
            return "Going out tonight, going out tonight"
        default:
            return "Living for the weekend"
        }
    }
}

let hardfi = HardFi()
hardfi.onThisDay(today) // "Living for the weekend"

today.next()
hardfi.onThisDay(today) // "Going out tonight, going out tonight"
~~~

As we have the same return value for multiple days, we can cut down the code by comparing the enum's `rawValue` (which acts, in this instance, like an array index) with a range of numbers. The above code is saying "if our enum is a `.Friday` or a `.Saturday` return "Going out tonight, going out tonight", otherwise return "Living for the weekend".

Let's use our `dailyActivity()` function and see exactly what the boys from Staines are up to.

~~~swift
// Error! cannot invoke 'dailyActivity' with an argument list of type '(day, HardFi)'
~~~

Oh, wait. We can't. Our `dailyActivity()` function only accepts a "person" if they are of the `CraigDavid` class. Let's dabble in some class inheritance to work around this limitation.

~~~swift
class Artist {
    let topSong: String
    let sneakers: String

    init(topSong: String, sneakers: String) {
        self.topSong = topSong
        self.sneakers = sneakers
    }

    func onThisDay(day: Day) -> String {
        return "Doing nowt"
    }
}
~~~

We've now created a new class called Artist. We also added some new properties to the Artist class to include each artists best song and preferred choice of sneakers (just go with it).

Next we need to add the Artist class as a superclass of our `CraigDavid` and `HardFi` classes (note we'll need to append override to our funcs in order to replace the default `onThisDay()` function in the `Artist` class – annoying, yes, but bear with me).

~~~swift
class CraigDavid: Artist {
    override func onThisDay(day: Day) -> String {
        // ...
    }
}

class HardFi: Artist {
    override func onThisDay(day: Day) -> String {
        // ...
    }
}

let craig = CraigDavid(topSong: "7 days", sneakers: "White")
craig.onThisDay(today) // "Making love"

let hardfi = HardFi(topSong: "Living for the Weekend", sneakers: "Adidas")
hardfi.onThisDay(today) // "Living for the weekend"
~~~

We can now change our `dailyActivity()` function to accept anything that has the class Artist.

~~~swift
func dailyActivity(var day: Day, person: Artist) {
    // ...
}

dailyActivity(today, hardfi) // Going out tonight, going out tonight / Going out tonight, going out tonight / Living for the weekend / Living for the weekend / Living for the weekend / Living for the weekend / Living for the weekend
~~~

This is good, but not great. It's still annoyingly restrictive. After all, it's not just artists who do things on a daily basis. Let's go ahead and create another class with someone else who famously got up to stuff during the week.

~~~swift
class God {
    func onThisDay(day: Day) -> String {
        switch day {
        case .Monday:
            return "Creating the heavens and the earth"
        case .Tuesday:
            return "Creating the sky"
        case .Wednesday:
            return "Creating dry land"
        case .Thursday:
            return "Creating the stars and heavenly bodies"
        case .Friday:
            return "Creating all that lives in water"
        case .Saturday:
            return "Creating all that lives on dry land"
        case .Sunday:
            return "Resting"
        }
    }
}

let jehovah = God()
jehovah.onThisDay(today)

dailyActivity(today, jehovah) // Error! cannot invoke 'dailyActivity' with an argument list of type '(day, God)'
~~~

Another error. Like before, our God class isn't compatible with the `dailyActivity()` function, which only accepts variables with the `Artist` class.

I suppose we could make God a subclass of Artist. But I don't think God wears sneakers.

Fortunately, this is where we get to use protocols. **YEAH!**

Let's create a protocol called `LivesByDay`, which requires the function `onThisDay()`, and reconfigure our other classes so they all conform to it (we can also remove the `override` keyword from our artist's `onThisDay()` functions).

~~~swift
protocol LivesByDay {
    func onThisDay(Day) -> String
}

class CraigDavid: Artist, LivesByDay {
    func onThisDay(day: Day) -> String {
        // ...
    }
}

class HardFi: Artist, LivesByDay {
    func onThisDay(day: Day) -> String {
        // ...
    }
}

class God: LivesByDay {
    func onThisDay(day: Day) -> String {
        // ...
    }
}
~~~

Easy! Now we can update our `dailyActivity()` function to accept anything that conforms to our `LivesByDay` protocol. This makes the function far more flexible without any awkwardness.

~~~swift
func dailyActivity(var day: Day, person: LivesByDay) {
    for _ in 1...7 {
        person.onThisDay(day)
        day.next()
    }
}

dailyActivity(today, jehovah)
// Creating all that lives in water / Creating all that lives on dry land / Resting / Creating the heavens and the earth / Creating the sky / Creating dry land / Creating the stars and heavenly bodies
dailyActivity(today, hardfi)
// Going out tonight, going out tonight / Going out tonight, going out tonight / Living for the weekend / Living for the weekend / Living for the weekend / Living for the weekend / Living for the weekend
dailyActivity(today, craig)
// Making love / Making love / Making love / Chilling / Meeting this girl / Taking her for a drink / Making love
~~~

We can now see what Craig David, Hard-Fi and God are all up to during the week.

And that is the power of programming.

---  


([View the complete Playground script on Github Gists](https://gist.github.com/fiveid/9647add7bcf9a7506e42))
