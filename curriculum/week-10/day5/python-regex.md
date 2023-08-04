# Regex

## Topics Covered / Goals

- Implement Regex methods in Python

## Lesson

### Regular Expressions (Regex)
- Regular Expressions, or regex, is the ability to use patterns to match character combinations. While every language has slightly different patterns to match these combinations, they fundamentally work the same
- It basically works like this:
  - A user inputs some text
  - Your compiler, using whatever language you decide, reads that input and pattern-matches it.
  - If it passes, do one thing. If it does not, do something else
- This is useful for something like phone number inputs or social security methods because people are terrible at putting their information into a form
  - For phone numbers, some people use parenthesis, others use periods, others use dashes, and others don't have any delimeters
  - Regex helps with this because they look for consistent patterns. In our case, we just want the numbers associated with a phone number input and to ignore the rest of the characters
- It's recommended for you to use `raw strings` instead of regular Python strings by prepending your string with `r` (e.g., `r("Hello\n\n")`). These raw strings will interpret the escaped characters as literally escaped characters, rather than Python's usual treatment of them as new line characters. **Let's get started on this**:

### Common usecases
- match a literal string
- require at least a certain number of characters (password length)
- validate SSN is valid (may or may not have dashes)
- validate a date is in the correct format

```python
import re # stands for Regular Expressions and brings in the Python re library into this file
## re.search() will search a string you pass in to see if it matches the regex pattern you have
string = 'June 24'
regex_pattern = r"([a-zA-Z]+) (\d+)" # any combination of letters, capital or not, followed by a space, followed by a digit of any length
print(re.search(regex_pattern, string)) # returns a `match` object with the thing that's matched up. This matches anywhere in the string
print(re.match(regex_pattern, string)) # same as above, but this matches the string starting from the beginning. only returns the first match
print(re.findall(regex_pattern, 'June 24 August 1 December 22')) # this returns a list of all matches
```
- Another example:
```python
import re
regex = r"[a-zA-Z]+ \d+"
matches = re.findall(regex, "June 24, August 9, Dec 12")
for match in matches:
    print(f"Full match: {match}")

## Find and replace
## replaced_string = re.sub(pattern, replacement_pattern, input_str, count, flags=0)
regex = r"([a-zA-Z]+) (\d+)"

print(re.sub(regex, r"\2 of \1", "June 24, August 9, Dec 12")) # 24 of June, 9 of August, 12 of Dec
```

## External Resources
- [Great Tutorial](https://www.datacamp.com/community/tutorials/python-regular-expression-tutorial)
- [Pythex](https://pythex.org/) Test your Python regex with this online tool.
- [Regex101](https://regex101.com)
- [Oldie but a goodie](https://blog.codinghorror.com/regular-expressions-now-you-have-two-problems/)
- [JS Regex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)
- [Data Camp Python Regex](https://www.datacamp.com/community/tutorials/python-regular-expression-tutorial) and [Learn Python](https://www.learnpython.org/en/Regular_Expressions)
- [Machine Learning Plus Python Regex](https://www.machinelearningplus.com/python/python-regex-tutorial-examples/)
- [Google's writeup on Python Regex](https://developers.google.com/edu/python/regular-expressions)

## Assignments
- [RegexOne](https://regexone.com/python) - highly recommended
- [Phone Numbers](https://github.com/sierraplatoon/algo-validate-phone) in Python Regular Expressions

