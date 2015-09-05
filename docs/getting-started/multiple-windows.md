# Multiple windows

This page was based in the examples available on the github folder: [Tutorial - Code Organization](https://github.com/UmSenhorQualquer/pyforms/tree/master/tutorials/3.CodeOrganization)


## **Create the Model**
***************************

This application will allow us to store and edit People information in a list.
Instead of showing you right a way how to develop the GUI I will suggest you how we can modularize our code in a in Model View Control (MVC) way.

First we will create our data model, which may be used outside the GUI.

### Start with the models

Lets start by creating the file Person.py where we will implement the model that will store the information about one single person.

```python
class Person(object):

	def __init__(self, firstName, middleName, lastName):
		self._firstName 	= firstName
		self._middleName 	= middleName
		self._lastName 		= lastName

	@property 
	def fullName(self):
		return "{0} {1} {2}".format(self._firstName, self._middleName, self._lastName)
```

After lets create the file People.py and implement the People class which will keep and manage the list of people.

```python
import pickle

class People(object):

	def __init__(self):
		self._people = []

	def addPerson(self, person):
		self._people.append(person)

	def removePerson(self, index):
		return self._people.pop(index)

	def save(self, filename):
		output = open(filename, 'wb')
		pickle.dump(self._people, output)

	def load(self, filename):
		pkl_file = open(filename, 'rb')
		self._people = pickle.load(pkl_file)
```

### Implement the GUI to manage ther Person model.

Create the file PersonWindow.py and implement the window that will allow us the edit the Person Model.

```python
import pyforms
from pyforms 			import BaseWidget
from pyforms.Controls 	import ControlText
from pyforms.Controls 	import ControlButton
from Person 			import Person

class PersonWindow(Person, BaseWidget):

	def __init__(self):
		Person.__init__(self, '', '', '')
		BaseWidget.__init__(self,'Person window')

		#Definition of the forms fields
		self._firstnameField 	= ControlText('First name')
		self._middlenameField  	= ControlText('Middle name')
		self._lastnameField  	= ControlText('Lastname name')
		self._fullnameField  	= ControlText('Full name')
		self._buttonField  		= ControlButton('Press this button')

		#Define the button action
		self._buttonField.value = self.__buttonAction


	def __buttonAction(self):
		self._firstName  			= self._firstnameField.value
		self._middleName 			= self._middlenameField.value
		self._lastName  			= self._lastnameField.value
		self._fullnameField.value 	= self.fullName
		
		#In case the window has a parent
		if self.parent!=None: self.parent.addPerson(self)

#Execute the application
if __name__ == "__main__":	 pyforms.startApp( PersonWindow )
```

**Note**: This window can be run as standalone application. 
If we is being developed by a team of developers we can distribute the windows implementation and test them indevidualy.

## Implement the GUI to manage ther People model.

Create the file PeopleWindow.py and implement the window that will allow us the manager the People Model.

```python
import pyforms
from pyforms 				import BaseWidget
from pyforms.Controls  		import ControlList
from People 				import People
from PersonWindow 			import PersonWindow
from AddMenuFuntionality 	import AddMenuFuntionality

class PeopleWindow(AddMenuFuntionality, People, BaseWidget):
	"""
	This applications is a GUI implementation of the People class
	"""
	
	def __init__(self):
		People.__init__(self)
		BaseWidget.__init__(self,'People window')

		#Definition of the forms fields
		self._peopleList	= ControlList('People', 
			plusFunction	= self.__addPersonBtnAction, 
			minusFunction	= self.__rmPersonBtnAction)
		
		self._peopleList.selectEntireRow = True

	def addPerson(self, person):
		"""
		Redifines the addPerson function from People class to update the GUI everytime a new person is added.
		"""
		super(PeopleWindow, self).addPerson(person)
		self._peopleList += [person._firstName, person._middleName, person._lastName]

	def __addPersonBtnAction(self):
		"""
		Add person button event
		"""
		win = PersonWindow()
		win.parent = self
		win.show()

	def __rmPersonBtnAction(self):
		"""
		Remove person button event
		"""
		self.removePerson( self._peopleList.mouseSelectedRowIndex )
		self._peopleList -= self._peopleList.mouseSelectedRowIndex

#Execute the application
if __name__ == "__main__":	 pyforms.startApp( PeopleWindow )
```

## 4. Implement the module that will give the Main Menu and the options save and load to the application.

AddMenuFuntionality.py
```python
class AddMenuFuntionality(object):
	...
```

## 5. Add the AddMenuFuntionality module to the PeopleWindow application, and the new functionalities will be added.

PeopleWindow.py
```python
class PeopleWindow(AddMenuFuntionality, People, BaseWidget):
	...
```