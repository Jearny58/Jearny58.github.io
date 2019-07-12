---
layout: post
published: false
title: Data Structures Using Python
---
Today we'll be taking a look at basic data structures in Python. The resource can be found [here](https://runestone.academy/runestone/books/published/pythonds/BasicDS/toctree.html). Without further ado, let's dive in!

## What Are Linear Structures?

- Stacks, queues, deques, and list = examples of data collections that depend on how they are added/removed
	- often referred to as **linear data structures**
    - once an item is added, stays in same position relative to other elements
    - have two "ends"
    - structures distinguised by the way items are added/removed

## What is a Stack?

- sometimes called "push-down stack"
- ordered collection of items where addition/removal of items always takes place at one end
- items in base of stack have been in the stack the longest (new items near top, older items near bottom)
- ordering principle = **LIFO, last-in first-out**
- Example = a stack of books
- fundamentally important, can be used to reverse the order of items
- another example = web browser and the back button (most recent sites first, older sites towards the bottom)

![stack_of_books.jpg]({{site.baseurl}}/img/stack_of_books.jpg)

## The Stack Abstract Data Type

- `Stack()` = creates new empty stack, no parameters needed
- `push(item)` = adds new item to top of stack, needs item and returns nothing
- `pop()` = removes top item from stack, needs no parameters, returns the item, and stack is modified
- `peek()` = returns top item from stack, does not remove, stack is not modified
- `isEmpty()` = tests to see if stack is empty, returns a boolean
- `size()` = returns number of items in stack, returns an integer

## Implementing a Stack in Python

- When we give abstract data type a physical implementation --> refer to it as a data structure

		class Stack:
        	def __init__(self):
         		self.items = []

     		def isEmpty(self):
         		return self.items == []

     		def push(self, item):
         		self.items.append(item)

     		def pop(self):
         		return self.items.pop()

     		def peek(self):
         		return self.items[len(self.items)-1]

     		def size(self):
         		return len(self.items)
                
- The above defines the class, must create a `Stack` object to use it

		from test import testEqual
		from pythonds.basic import Stack

		def revstring(mystr):
        	myStack = Stack()
            for ch in mystr:
            	myStack.push(ch)
            revstr = ''
            while not myStack.isEmpty():
            	revstr = revstr + myStack.pop()
            
            return revstr

		testEqual(revstring('apple'),'elppa')
		testEqual(revstring('x'),'x')
		testEqual(revstring('1234567890'),'0987654321')
        
## Simple Balanaced Parentheses

- **Balanced parentheses** = each opening symbol has corresponding closing symbol, pairs of parentheses are properly nested

		from pythonds.basic import Stack

		def parChecker(symbolString):
    		s = Stack()
    		balanced = True
    		index = 0
    		while index < len(symbolString) and balanced:
        		symbol = symbolString[index]
        		if symbol == "(":
            		s.push(symbol)
        		else:
            		if s.isEmpty():
                		balanced = False
            		else:
                		s.pop()

        		index = index + 1

    		if balanced and s.isEmpty():
        		return True
    		else:
        		return False

		print(parChecker('((()))')) #True
		print(parChecker('(()')) #False

- `parChecker` function assumes `Stack` class is available and returns boolean to indicate if string of parentheses are balanced

## Converting Decimal Numbers to Binary Numbers

- all values stored within computer exist as string of binary digits (string of 0s and 1s)
- How can we easily convert integer values into binary numbers?
	- "Divide by 2" algorithm that uses a stack to keep track of digits for the binary result
    - continually dives number by 2, keeps track of remainder
    
    		from pythonds.basic import Stack

			def divideBy2(decNumber):				
        		'''implementation of divide by 2 algorithm'''
    			remstack = Stack()

    			while decNumber > 0:
        			rem = decNumber % 2			# uses modulo operator to extract remainder
        			remstack.push(rem)			# pushes remainder into stack
        			decNumber = decNumber // 2

    			binString = "" 					# create empty string
    			while not remstack.isEmpty():	
        			binString = binString + str(remstack.pop())		# binary digits appended to right-hand to string one at a time 

    			return binString

			print(divideBy2(42))
            
- above algorithm can be extended to perform conversion for any base
- most common encodings: octal (base 8) and hexadecimal (base 16)

		from pythonds.basic import Stack

		def baseConverter(decNumber,base):
    		digits = "0123456789ABCDEF"

    		remstack = Stack()

    		while decNumber > 0:
        		rem = decNumber % base
        		remstack.push(rem)
        		decNumber = decNumber // base

    		newString = ""
    		while not remstack.isEmpty():
        		newString = newString + digits[remstack.pop()]

    		return newString

		print(baseConverter(25,8))
		print(baseConverter(26,26))
        
- the above function takes a number and base and outputs that respective value expressed as that base
	- example: What is the value of 25 expressed as an octal (base 8) number? Answer: 31 (see first equation)
    
## Infix, Prefix and Postfix Expressions

- **infix** = type of notation where operator is in between the two operands being worked on
- operators have a **precedence** level
	- i.e. operators of higher precedence are used before operators of lower precedence
    - parentheses only thing that can change order
- may be obvious to humans, but computers need to know exactly what to perform and in what order
- **fully parenthesized** expression --> uses on pair of parentheses for each operator
- **prefix** & **postfix**, all operators precede or come after the corresponding operands, respectively

## Conversion of INfix Expression to Prefix and Postfix

