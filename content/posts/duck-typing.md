---
title: "Duck Typing in Python"
date: 2020-09-07T06:20:14+09:30
draft: false
toc: false
images:
tags:
  - python
---


In OOP (Object Oriented Programming) there is a concept called Polymorphism which allow the use of objects of different types through a uniform interface.

Since Python allow OOP constructs Polymorphism applies. 

As Python is a dynamic type language a method didn’t using an interface does not need to know about about the interface type, only it's abstract details. The interface will work just by making suer the arguments match when it is used.

This Polymorphism in Python is called duck typing. 

Duck typing is in turn named after the “duck test”, attributed to [James Whitcomb Riley](https://en.wikipedia.org/wiki/James_Whitcomb_Riley), the American poet.

> "When I see a bird that walks like a duck and swims like a duck and quacks like a duck, I call that bird a duck."

Better to demonstrate that with an example.

This I got from the book `The Python - Apprentice`. This actually an excellent series of 3 books where you can get it [here](https://leanpub.com/b/python-craftsman)

Lets say you have a `Flight` `class` which records data of a flight and one of the methods is to print the boarding pass of the passengers

So a decoupled implementation of the `make_board_pass` method would be:

```python
class Flight:

# ...
  def make_boarding_cards(self, card_printer):
    for passenger, seat in sorted(self._passenger_seats()):
      card_printer(passenger, seat, self.number(), self.aircraft_model())
```

Given the duck typing of Python we can write a `card_printer` function that would work "inside" the `make_boarding_cards` method and it does not need to know the type of the `card_printer` object as long it looks like a card_printer interface it will be treated as one.

```python
def console_card_printer(passenger, seat, flight_number, aircraft):
  output = "| Name: {0}" \
      " Flight: {1}" \
      " Seat:{2}" \
      " Aircraft: {3}" \
      " |".format(passenger, flight_number, seat, aircraft)
  banner = '+' + '-' * (len(output) - 2) + '+' border = '|' + ' ' * (len(output) - 2) + '|' lines = [banner, border,   output, border, banner] card = '\n'.join(lines)
  print(card) 
  print()
```

So then we can inject the `console_card_printer` in the `make_boarding_cards` method

```python
from airline import Flight, console_card_printer

f = Flight("BA758", Aircraft("G-EUPT", "Airbus A319",
num_rows=22, num_seats_per_row=6)) 

f.allocate_seat('12A', 'Guido van Rossum')

f.make_boarding_cards(console_card_printer)
```

```bash
+-------------------------------------------------------------------------+ 
|                                                                         | 
| Name: Guido van Rossum Flight: BA758 Seat: 12A Aircraft: Airbus A319    |
|                                                                         |
+-------------------------------------------------------------------------+
```

The advantage of duck typing is that we can design a more loosely coupled program.

For example if later needed one can write N different card_priters that will produce a different output, say a printer that output a html format or a markdown instead of a console output, and still work without the necessity to change the make_boarding_cards method.