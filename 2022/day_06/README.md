# --- Day 6: Tuning Trouble ---

## The Problem

### --- Part One ---
The preparations are finally complete; you and the Elves leave camp on foot and begin to make your way toward the star fruit grove.

As you move through the dense undergrowth, one of the Elves gives you a handheld device. He says that it has many fancy features, but the most important one to set up right now is the communication system.

However, because he's heard you have significant experience dealing with signal-based systems, he convinced the other Elves that it would be okay to give you their one malfunctioning device - surely you'll have no problem fixing it.

As if inspired by comedic timing, the device emits a few colorful sparks.

To be able to communicate with the Elves, the device needs to lock on to their signal. The signal is a series of seemingly-random characters that the device receives one at a time.

To fix the communication system, you need to add a subroutine to the device that detects a start-of-packet marker in the datastream. In the protocol being used by the Elves, the start of a packet is indicated by a sequence of four characters that are all different.

The device will send your subroutine a datastream buffer (your puzzle input); your subroutine needs to identify the first position where the four most recently received characters were all different. Specifically, it needs to report the number of characters from the beginning of the buffer to the end of the first such four-character marker.

For example, suppose you receive the following datastream buffer:

mjqjpqmgbljsphdztnvjfqwrcgsmlb
After the first three characters (mjq) have been received, there haven't been enough characters received yet to find the marker. The first time a marker could occur is after the fourth character is received, making the most recent four characters mjqj. Because j is repeated, this isn't a marker.

The first time a marker appears is after the seventh character arrives. Once it does, the last four characters received are jpqm, which are all different. In this case, your subroutine should report the value 7, because the first start-of-packet marker is complete after 7 characters have been processed.

Here are a few more examples:
- bvwbjplbgvbhsrlpgdmjqwftvncz: first marker after character 5
- nppdvjthqldpwncqszvftbrmjlhg: first marker after character 6
- nznrnfrfntjfmvfwmzdfjlvtqnbhcprsg: first marker after character 10
- zcfzfwzzqfrljwzlrfnpqdbhtmscgvjw: first marker after character 11

How many characters need to be processed before the first start-of-packet marker is detected?

### --- Part Two ---
Your device's communication system is correctly detecting packets, but still isn't working. It looks like it also needs to look for messages.

A start-of-message marker is just like a start-of-packet marker, except it consists of 14 distinct characters rather than 4.

Here are the first positions of start-of-message markers for all of the above examples:
- mjqjpqmgbljsphdztnvjfqwrcgsmlb: first marker after character 19
- bvwbjplbgvbhsrlpgdmjqwftvncz: first marker after character 23
- nppdvjthqldpwncqszvftbrmjlhg: first marker after character 23
- nznrnfrfntjfmvfwmzdfjlvtqnbhcprsg: first marker after character 29
- zcfzfwzzqfrljwzlrfnpqdbhtmscgvjw: first marker after character 26

How many characters need to be processed before the first start-of-message marker is detected?

## The Solution

### --- Part One ---
This was fairly straightforward, but I made it a bit more complex than I should have when first attempting it. I tried to make a `std::unordered_map<char, int>` to keep count of how many of a given character was in a certain substring, but that lead to a lot of problems. I then changed my method to use a `std::unordered_set<char>` instead. I had a pointer integer that pointed to the first character of the substring, then created a substring of size 4 from that pointer variable. Then, for each character in the substring, if it was already in the set, I broke from the loop, since all the characters could not be unique. If it wasn't in the set, I added it. Then, at the end of the loop, if `allUnique` was not set to false, then all the characters in the substring had to be unique. So, I returned the value of the pointer variable, plus 4.

### --- Part Two ---
This part was the same as the last part, except the length of the substring had to be equal to 14. Instead of writing an entirely different function, I modified my existing function to take another parameter, `s`, which is the size of the repeating substring to look for. Because the outer loop executes for every character in the input string in the worst case, the inner loop executes however long the substring is, and searching an unordered_set operates in constant time, the time complexity of the function is $O(ns)$, where $n$ is the length of the input string, and $s$ is the size of the substring.

I also modified the function a bit after my initial submission, because I'm a bit of a perfectionist. I changed the function even more to not use another string variable at all. I instead created an inner loop with another index variable and tried referencing the input string at index `p + i`. If a `std::out_of_range` exception was thrown, I knew that the end of the input string had been reached without finding a non-repeating substring, so I returned -1. 

I then came back again and wrote the same function using iteratbles and templates. This way, it could be used on any object with an iterator. Then, I thought that the template looked ugly, so I instead used pointers. As a result, it can only be used on data structures in which the values lie on consecutive memory spaces, such as c strings, arrays, vectors, and `std::string`s.
