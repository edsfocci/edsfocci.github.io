---
layout: post
title:  "Lightning migration: The anatomy of JavaScript Button code"
date:   2020-01-31 16:57:47 -0500
categories: salesforce lightning javascript buttons
---
In my last blog post, [Lightning migration: JavaScript Buttons][lightning_migration_javascript_buttons], I discussed the problems involved when you are using JavaScript buttons in your Salesforce org, especially when it comes to migrating to the Lightning Experience UI. I also wrote about a tool you can use to help convert some of the simpler JavaScript buttons. But what can you do if the tool fails? In this post, I will walk you through the code in a JavaScript button, so that you can hopefully do some of your JavaScript button conversions yourself.

How do we find these JavaScript buttons? You will need to go to the "Buttons, Links, and Actions" of the particular Object (like Account or Opportunity, for example) that the JavaScript button was designed for. The JavaScript buttons are indicated by the "Content Source": "OnClick JavaScript". This works in both Lightning Experience and Classic UI; see the screenshots below for examples.

![Buttons, Links, and Actions - Classic UI](/assets/Buttons_Links_Actions-Classic.png)
Buttons, Links, and Actions - Classic UI

![Buttons, Links, and Actions - Lightning Experience UI](/assets/Buttons_Links_Actions-LightningExperience.png)
Buttons, Links, and Actions - Lightning Experience UI

When you open the JavaScript button you are trying to convert, you will see a field that contains JavaScript code.

![Edit JavaScript Button page](/assets/Edit_JavaScript_Button.png)
Edit JavaScript Button page

Here is the JavaScript code I will walk through so that you can make the conversions to make a quick action that is compatible with the Lightning Experience UI:

{% raw %}
{!REQUIRESCRIPT("/soap/ajax/26.0/connection.js")}
{!REQUIRESCRIPT("/soap/ajax/26.0/apex.js")}

var resp = sforce.apex.execute("OppHelper","RenewOpp",{oppId:'{!Opportunity.Id}'});
var url = '/006/e?CF00Ni000000EpsgO_lkid=' + resp[0] + '&00Ni000000EpsgY=' + resp[1];

window.open(url,'_self');
{% endraw %}

Here is the first line:

{% raw %}
{!REQUIRESCRIPT("/soap/ajax/26.0/connection.js")}
{% endraw %}

You don't have to do anything about this line. You just need to know that it is a required line for JavaScript buttons, to establish a connection between the JavaScript code and Salesforce.

{% raw %}
{!REQUIRESCRIPT("/soap/ajax/26.0/apex.js")}
{% endraw %}

You don't need to do anything about this line either, except know that this line allows you to run Apex code that is exposed as a SOAP web service. I won't go into details on this post, but you can read the [Webservice Methods][webservice_methods] Apex Developer documentation for more information.

{% raw %}
var resp = sforce.apex.execute("OppHelper","RenewOpp",{oppId:'{!Opportunity.Id}'});
{% endraw %}

This line of JavaScript code runs the "RenewOpp" method of the Apex class "OppHelper", setting the "oppId" parameter of that method to the Opportunity record's Id. For converting your JavaScript button, seeing a similar line tells me that, depending on what the rest of the JavaScript code looks like, at best I will need to run that Apex code from a Lightning Flow, if the rest of the code doesn't need to refresh the page or redirect to a different Salesforce page or webpage. At worst, I will need some coding skills to create a Lightning Web Component or Aura Component, if the rest of the code needs to refresh the page or redirect to a different Salesforce page or webpage. I will show all these scenarios in a future blog post.

Another thing to note is that the results of the Apex method being run is being stored in the "resp" JavaScript variable.

{% highlight javascript %}
var url = '/006/e?CF00Ni000000EpsgO_lkid=' + resp[0] + '&00Ni000000EpsgY=' + resp[1];
{% endhighlight %}

This line of JavaScript code is forming a path to create a new Opportunity record. This line is actually an URL hack that I talked about in my previous blog post. What an URL hack does is takes fields from the "Edit" or "New" Object record page, and fills them with values defined on the path. They are set like this:

{% raw %}
{ Object field reference by field Id }={ fill-in value }
{% endraw %}

The URL hack is using the results of the Apex method, stored in the "resp" variable, to set or pre-set fields for creating or editting a record. The "resp" variable is holding an array, which stores multiple values. You can tell because you see square brackets "[]" with a number in between those square brackets. Many programming languages has some version of array, but it is probably safe to say that "resp[0]" holds a different value than "resp[1]".

Also, the "006" has a particular meaning in Salesforce. It is a key prefix, which is an identifier to indicate which Salesforce Object a record will be from the first 3 characters, from the left, of the record Id. The Salesforce Ben website has an [excellent article][key_prefixes] to tell you what each key prefix means. "006" in this case indicates the Opportunity Salesforce Object. Since it is only 3 characters long, this is the path to create a new Opportunity record. If it is more characters long, like 15 characters, then the path is to edit an existing Opportunity record.

{% highlight javascript %}
window.open(url,'_self');
{% endhighlight %}

This line of JavaScript code tells me that a path or webpage will be opened. If it is a path, it will take you to a different Salesforce page. If it is a website URL, then you will be taken to a webpage outside of Salesforce. Either way, seeing this line tells me that I will need to create a Lightning Web Component (LWC) or Aura Component to convert this JavaScript button. You can use either, and I prefer using LWCs, to create the component that will open a webpage outside of Salesforce. You can also navigate to Salesforce pages using either LWCs and Aura Components. However, LWCs are still new, and so if there are navigation functionality that is missing while developing with LWC, be prepared to develop with Aura Components instead. You can view the docs on navigation functionality for [LWCs][lwc_navigation] and [Aura Components][aura_navigation].

In a future blog post, I will go into the details of using quick actions, Lightning Flow, Lightning Web Components, and Aura Components to create the elements you may need to perform your JavaScript button conversion. Stay tuned!

[lightning_migration_javascript_buttons]: http://edsfocci.github.io/salesforce/lightning/javascript/buttons/2019/10/21/lightning-migration-javascript-buttons.html
[webservice_methods]: https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_web_services_methods.htm
[key_prefixes]: https://www.salesforceben.com/salesforce-object-key-prefix-list/
[lwc_navigation]: https://developer.salesforce.com/docs/component-library/bundle/lightning-navigation/documentation
[aura_navigation]: https://developer.salesforce.com/docs/component-library/bundle/lightning:navigation/documentation
