# REST-JSon-Helpers

The unit provides some helper classes to make serializing classes into JSON a bit easier.

## JsonUTCDate
The _JsonUTCDate_ attribute applied to ca field results in the field value being converted into UTC format when serialized into JSON format. Of course the conversion is reverted when the field is set from JSON. If the field value equals _cNoDate_ (i.e. "0000-00-00T00:00:00.000Z") conversion from/into local time format is skipped. When writing such a value it will result in a JSON _null_ object value.

Usage:
```Delphi
type
  TMyClass = class
  private
    [JsonUTCDate]
    FStartDate: TDateTime;
    [JsonUTCDate]
    FEndDate: TDateTime;
    ...
  end;
```

## JsonStrings
The _JsonStrings_ attribute applied to a _TStrings_ or descendant will serialize the strings to an array of string and vice versa. Note that it should always be accompanied by _JSONOwned(False)_.

Usage:
```Delphi
type
  TMyClass = class
  private
    [JSONOwned(False), JSONStrings]
    FLines: TStringList;
    ...
  end;
```
## JsonObjectList\<T>
_JsonObjectList\<T>_ is a generic attribute that must be derived to resolve the generic. The derived attribute can then be applied to a _TObjectList\<T>_ descendant. It makes the list being serialized to an array of object and back again.
  
Each field holding such a list requires a _JSONOwned(False)_ attribute to avoid the list instance being destroyed during deserialisation.
  
Usage:
```Delphi
type
  TContact = class
    ...
  end;

  JsonObjectListContactAttribute = class(JsonObjectList<TContact>); { the class keyword is mandatory }
  
type  { this type keyword is mandatory }
  [JsonObjectListContact]
  TContactList = class(TIdentList<TContact>);

type
  TMyAddressBook = class
  private
    [JSONOwned(False)] { this attribute is mandatory to avoid FContacts being destroyed }
    FContacts: TContactList;
    ...
```
The new attribute (_JsonObjectListContactAttribute_) as well as the new list (_TContactList_)  must **not** be an alias - the **class** keyword is mandatory. Also there must be at least one **type** keyword between the declaration and the use of the attribute.

**Note:** The previous version required the declaration of an TObjectListInterceptor\<T> descendant, which was given to a non-generic version of the attribute. The new approach is much easier to use.

## TConvert
_TConvert_ is a record providing some helpful methods to convert to and from Json. In addition to Json objects it can also produce Json arrays, as well as revert them with its _FromJSONArray_ methods.
