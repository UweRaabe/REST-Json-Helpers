# REST-JSon-Helpers

Some helpers for serializing objects to Json and back using the standard Delphi library.

The unit provides some helper classes to make serializing classes into JSON a bit easier.

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

_JsonObjectList_ is an attribute that should be applied to a _TObjectList<T>_ instance or descendant. It makes the object being serialized to an array of object and back again. It needs an interceptor class derived from _TObjectListInterceptor<T>_ as parameter. 
It also requires a _JSONOwned(False)_ companion.
  
Usage:
```Delphi
type
  TContactList = TIdentList<TContact>;
  TContactListInterceptor = TObjectListInterceptor<TContact>;

type
  TMyAddressBook = class
  private
    [JSONOwned(False), JsonObjectList(TContactListInterceptor)]
    FContacts: TContactList;
    ...
```
The alias _TContactList_ is actually not needed, but the _TContactListInterceptor_ is mandatory - as well as at least one _type_ keyword between the alias declaration and the use of the alias inside the attribute.
