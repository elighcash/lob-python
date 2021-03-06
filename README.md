lob-python
==========

[![Build Status](https://travis-ci.org/lob/lob-python.png?branch=master)](https://travis-ci.org/lob/lob-python)

This is the python wrapper for the lob.com API.

This wrapper works in the object oriented style, that is, to make calls you have to call the method on a class and the
return types are python objects. To get a `dict` on any object, you can call the `to_dict()` method of the object.

Installation
============

You can use `pip` or `easy_install` for installing the package.

```
pip install lob-python
easy_install lob-python
```

Usage
======

## Intialization and Configuration

To initialize the wrapper, import `lob` and set the `api_key`

```python
import lob
lob.api_key = 'your-api-key'
```

## Addresses

Addresses work with the `Address` class and the objects returned are of this class.

### Create a new address

```python
address = lob.Address.create(name='Siddharth Saha', address_line1='104, Printing Boulevard',
                             address_city='Boston', address_state='MA', address_country='US',
                             address_zip='12345')
print address.to_dict()
```

You can pass optional parameters as well while creating an address

```python
print lob.Address.create(name='Siddharth Saha', address_line1='104, Printing Boulevard',
                         address_line2='Sunset Town', email='sidchilling@gmail.com',
                         address_city='Boston', address_state='MA', address_country='US',
                         address_zip='12345').to_dict()
```

### List Addresses

Will return a `list` of `Address` objects

```python
lob.Address.list()
```

You can also pass `count` and `offset` parameters (or either one of them)

```python
lob.Address.list(count=5, offset=2)
```

### Find an Address

You can query an address with its `ID` and you will get an `Address` object

```python
print lob.Address.get(id = '<your-address-id>').to_dict()
print lob.Address.get(id = lob.Address.list(count=1)[0].id).to_dict()
```

### Delete an Address

You can delete an address with its `ID`

```python
lob.Address.delete(id='<your-address-id>')
```

## Address Verification

You can verify an Address - this API will call return a `LobObject` which is
the super-class of all other classes. You can of course do a `to_dict()` and
get the `dict` representation of a `LobObject` as well.

```python
verify = lob.AddressVerify.verify(name='Siddharth Saha', email='sidchilling@gmail.com',
                                  address_line1='220 William T Morrissey', address_city='Boston',
                                  address_state='MA', address_zip='02125', address_country='US')

print verify.to_dict()
```

## Setting

Works on the `Setting` class.

### List all Settings

This will return a `list` of `Setting` objects.

```python
print lob.Setting.list()
```

### Find a Setting

```python
print lob.Setting.get(id = '<setting-id>').to_dict()
print lob.Setting.get(id = lob.Setting.list()[0].id).to_dict()
```

## Services

Works on the `Service` class.

### List all Services

Returns a `list` of `Service` objects

```python
print lob.Service.list()
```

## Packaging

Works on the `Packaging` class.

### List all packagings

Returns a `list` of `Packaging` objects

```python
print lob.Packaging.list()
```

## Objects

Works on the `Object` class.

```python
lob.Object.list() # Returns a list of Object objects
lob.Object.list(count = 4, offset = 2) # Can specify count and offset
lob.Object.delete(id = '<your-object-id>') # Delete an object via it's ID
lob.Object.create(name = 'Siddharth Saha', file = 'https://www.lob.com/goblue.pdf', 
                         setting_id = '<setting-id>', quantity = 1) # Will create an object and return its instance
```

## Jobs

Works on the `Job` class.

```python
lob.Job.list() # Returns a list of Job objects
lob.Job.list(count = 5, offset = 1) # Can specify count and offset as well
lob.Job.list(count = 5) # Can specify either offset or count as well
lob.Job.get(id = '<job-id>') # Can find a Job based on its ID - Returns a Job instance
```

### Creating Jobs

Will return a `Job` instance if creation is successful

```python
print lob.Job.create(name = 'Siddharth First Job', to = lob.Address.list(count = 1)[0].id, 
                     objects = lob.Object.list()[0].id, 
                     from_address = lob.Address.list(count = 1, offset = 5)[0].id).to_dict()
```

As in the above call, you can see `to` and `from_address` are `Address` IDs and `objects` is a `Object` ID. You can specify these differently as well - passsing complete address parameters. Also, `objects` can be a list specifiying multiple `object` IDs or `object` parameters as well. The following code block will show each of these possibilities.

```python
objects = [lob.Object.list()[0].id, {'name' : 'Siddharth Job Object', 
                                     'file' : 'https://www.lob.com/goblue.pdf', 
                                     'setting_id' : lob.Setting.list()[0].id, 
                                     'quantity' : 1}] # The objects list can contain both object id as well as parameters

from_addrsss = {'name' : 'Siddharth Saha', 
                'address_line1' : '220 William T Morrissey', 
                'address_line2' : 'Sunset Town', 
                'address_city' : 'Boston', 
                'address_state' : 'MA', 
                'address_country' : 'US', 
                'address_zip' : '02125'}

print lob.Job.create(name = 'Siddharth Second Job', to = lob.Address.list(count = 1)[0].id, 
                            objects = objects, from_address = from_address, 
                            packaging_id = lob.Packaging.list()[0].id, 
                            service_id = lob.Service.list()[0].id).to_dict()
```

The above code block also shows optional parameters that can be passed

## Postcard

Works on the `Postcard` class.

```python
lob.Postcard.list() # Returns a list of Postcard objects
lob.Postcard.list(count = 5, offset = 3) # Can also pass count and offset
```

### Creating a postcard

You must either specify the `message` argument or the `back` argument (but not both). Both `to` and `from_address` addresses can contain Address ID or Address parameters (as in creation of Job).

```python
# Specifying message
print lob.Postcard.create(name = 'Siddharth Test Postcard', to = lob.Address.list(count = 1)[0].id, 
                           message = 'This is a standard test message', 
                           front = 'https://www.lob.com/postcardfront.pdf', 
                           from_address = lob.Address.list(count = 1, offset = 4)[0].id).to_dict()

# Specifying back and address as parameters (using from_address defined earlier in Job creation)
print lob.Postcard.create(name = 'Siddharth New Test Postcard', to = lob.Address.list(count = 1)[0].id, 
                          front = 'https://www.lob.com/postcardfront.pdf', 
                          back = 'https://www.lob.com/postcardback.pdf', from_address = from_address)
```
