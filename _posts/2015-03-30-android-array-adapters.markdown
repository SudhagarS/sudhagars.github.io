---
layout: post
title:  "Android Array Adapters — What most of the tutorials don’t tell you"
date:   2015-03-30
categories: android
tags: android
excerpt: A deep dive into Android array adapters.
---

Do a quick google search on the topic array adapter, custom array adapter, implementing array adapter, android custom list view, or extending array adapter. You would find that most of the results are tutorials on how you could extend the ArrayAdapter, that is a part of Android SDK, and implement your own custom array adapters. The thing with most of these tutorials is all of them get some of the basic things wrong.

## Adapter

What is an *Adapter* anyway? An adapter is an object that provides views for a list view. Whenever list view needs to draw a view at a particular list position, it gets it from the adapter. The adapter makes the view for that position and returns it. Since the adapter creates the views, it is necessary for it to also store the underlying data. So the adapters are all about this. **They pack data and the logic for creating views out of the data.** The data can be a cursor or a list of objects. To support these different types, we have CursorAdapter and ArrayAdapter in the SDK.

## ArrayAdapter

Though it is called ArrayAdapter, it actually stores the data in a list. Well, why use an array when you can use a list.
Here is a snippet from ArrayAdapter.java.

{% highlight java %}
/**
Contains the list of objects that represent the data of this ArrayAdapter. The content of this list is referred to as “the array” in the documentation.
*/
private List mObjects;
{% endhighlight %}

ArrayAdapter class has `getView()` method that is responsible for creating the views. So behind the scenes, a listview calls this method to get a view for a particular position.
The `getView()` method can inflate the view from a layout resource or create it programmatically. It expects the inflated view to be a TextView. If it turns out to be a `ViewGroup`, then it is necessary for the view group to have a text view.

This text view’s id should be passed to the adapter when it is instantiated, so that it can retrieve it by doing `findViewById()`. After that the data object for given position is retrieved from the list. The data object is converted to a String by running toString on it and the string value is set to the text view.

{% highlight java %}
try {
  if (mFieldId == 0) {
    //  If no custom field is assigned, assume the whole resource is a TextView
      text = (TextView) view;
    } else {
      //  Otherwise, find the TextView field within the layout
      text = (TextView) view.findViewById(mFieldId);
    }

} catch (ClassCastException e) {
  Log.e("ArrayAdapter", "You must supply a resource ID for a TextView");
  throw new IllegalStateException("ArrayAdapter requires the resource ID to be a TextView", e);
}
T item = getItem(position);
if (item instanceof CharSequence) {
  text.setText((CharSequence)item);
} else {
  text.setText(item.toString());
}

return view;
{% endhighlight %}

### ArrayAdapter — Constructors

Here are the constructor methods in the ArrayAdapter class

1. ArrayAdapter(Context context, int resource)
2. ArrayAdapter(Context context, int resource, int textViewResourceId)
3. ArrayAdapter(Context context, int resource, T[] objects)
4. ArrayAdapter(Context context, int resource, int textViewResourceId, T[] objects)
5. ArrayAdapter(Context context, int resource, List objects)
6. ArrayAdapter(Context context, int resource, int textViewResourceId, List objects)

All these methods have two parameters in common, `context` and `resource`. `resource` is ID of the layout resource that getView() would inflate to create the view. If the layout’s root is any view group instead of just a text view, the parameter `textViewResourceId` should also be passed so that getView() can retrieve the text view and set its text.

`context` refers to the activity where the adapter is created. It is helps you with accessing system services and resources in case you need them.

`object` is a collection of objects that provide data to the adapter. If it is a list, the adapter stores it as is. If it is an array, the adapter converts it to a list. It is not required to pass this value when instantiating the adapter. It can be set later using `addAll()` method.

Other useful methods in the ArrayAdapter class:

1. add() — add an object to the collection
2. remove() — remove an object from the collection
3. getItem(int pos) — return the object at position, pos
4. getContext() — get the context

That’s all about array adapters. We could see that the limitation here is whatever type of objects we pass, the adapter is going to run `toString()` on it and set the string to text view. This is ok if the objects are just strings. But the things that are rendered in a list view are more than a list of strings.

## Custom Array Adapter

List items in a common list view contain complex layout structure than just a simple text view. To support this complex layouts, we need to customize array adapters through inheritance.

To get started create a new class, say `MyArrayAdapter`, and have it extend `ArrayAdapter`. Since ArrayAdapter class has no default constructor, we need to define a constructor that matches one of the six constructors in the superclass.

{% highlight java %}
public class MyArrayAdapter extends ArrayAdapter
{
    private final int mResource;

    public MyArrayAdapter(Context content, int resourece, List objects)
    {
      super(context, objects);
      mResource = resource;
    }
}
{% endhighlight %}


There is no need to pass resource parameter to super constructor as the super class(ArrayAdapter) uses it only in `getView()` method which we are going to override anyway.

Now we do the core part of overriding the `getView()` method. In `getView()`, inflate a view from layout resource or you can also do it programmatically. Your views can be as complex as you want. Once the view is inflated we need to fill it up with data.

To access the data object call `getItem(position)`, then call methods on the object to get the values. Finally set these values on different UI elements in the layout and return the view.

Here is a sample:

{% highlight java %}
@Override
public View getView(int position, View convertView, ViewGroup parent) {
    LayoutInflater inflater = (LayoutInflater) getContext().
            .getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    View rowView = inflater.inflate(mResource, parent, false);
    TextView nameView = (TextView) rowView.findViewById(R.id.name);
    TextView marksView = (TextView) rowView.findViewById(R.id.marks);
    Student s = getItem(position);
    nameView.setText(s.getName());
    markView.setText(s.getMark());
    return rowView;
}
{% endhighlight %}

This is the least optimized implementation of `getView()` as it ignores the `convertView` that the list view passes. A convert view is a view that is currently not in the screen and hence can be recycled. This results in big performance boost as we reuse an existing view instead of inflating a new view from the xml layout file which is a relatively costly operation.

To get the fastest list view possible, use convert view if it is not null and also use a pattern called view holder pattern that helps you in saving also those `findViewById()` calls for recycled views.

## The wrong way
Lets summarise everything we did to set up a custom array adapter:

1. Create a class by extending ArrayAdapter
2. Add a constructor to the class
3. Override getView method

It seems so simple, right? However most of tutorials I read online have got it wrong. Most of the android beginners do it wrong.

Here is where it is done wrong.

{% highlight java %}
public class MyArrayAdapter extends ArrayAdapter<Student> {
    private final Context mContext;
    private final Student[] mValues;
    private final int mResource;

    public MyArrayAdapter(Context context, int resource, Student[] values) {
        super(context, resource, values);
        mContext = context;
        mValues = values;
        mResource = resource;
    }
    ...
}
{% endhighlight %}

Here super class constructor is passed context and values, i.e. the responsibility of holding references to context and values is delegated to the super class. Hence there is no need for MyArrayAdapter to hold references to these. By calling `getContext()` and `getItem(pos)`, `MyArrayAdapter` class can access these values.

ArrayAdapter class does not have any methods to get the layout resource id. Therefore it totally makes sense to have a member variable for that here.

In some examples, the resource id is not the part of the custom adapter class. It is just directly hardcoded into the getView method. It is a bad practice and should be avoided.

> View view = inflater.inflate(R.layout.rowlayout, parent, false);

Note that in the code above, the values are stored in a array. This results in loss of dynamic behavior. If the list items are dynamic it is better to store the data in a list.

Even if the values are stored in a list, you need ways to add or remove items from the list. Hence many custom adapters have add() and remove() methods. 

{% highlight java %}
public class MyArrayAdapter extends ArrayAdapter<Student> {
    ...

    public void add(Student s) {
        values.add(s);
    }

    public void remove(Student s) {
        values.remove(s);
    }
}
{% endhighlight %}

Again, ArrayAdapter has these methods and they are thread safe too. If you have avoided storing a reference of data in custom adapter in the first place, you wouldn’t have needed these methods.

It is a always a good practice to go through the documentation of a class (or the source code) you are going to customize. There is a good chance it has the methods you need. 

Happy hacking!