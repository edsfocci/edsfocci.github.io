---
layout: post
title:  "Lightning migration: JavaScript Buttons"
date:   2019-10-21 03:49:51 -0500
categories: salesforce lightning javascript buttons
---
If your Salesforce org has been in existence for a good number of years, and your company is still using the Salesforce Classic UI, you might be considering migrating to the Lightning Experience UI. As part of migrating to Lightning, you'll need to consider whether your org uses JavaScript buttons, and what you can do to replace them. This post will help you with strategies to replace those JavaScript buttons, to help you in your Lightning migration, and to ensure no current functionality is lost after your migration.

"Why must I replace my JavaScript buttons?" you ask. Well, JavaScript buttons are incompatible with the Lightning UI. From the Trailhead module, [Lightning Alternatives to JavaScript Buttons][lex_javascript_button_migration]{:target="_blank"}, JavaScript buttons won't work in Lightning, because JavaScript buttons pose a security risk, possibly allowing the JavaScript code to access data and make it available to other components or users who shouldn't have access to them. When designing the architecture of the Lighning UI, it was decided that Lightning components will be isolated from each other, unless the components are explicitly designed to interact in approved secure ways, which is what Salesforce calls Lightning Locker. This allows greater control over which components have access to other components' data, which makes everything more secure.

From a user perspective, if you see JavaScript buttons in your Classic page layouts, when you move to Lightning, you will notice that those buttons are missing. So, as your org's administrator or developer, you will need to take steps to replace those JavaScript buttons so that you won't lose their functionality when you officially migrate to Lightning.

The first thing you should do is run the [Lightning Experience Configuration Converter][lcc_javascript_buttons_scan]{:target="_blank"} tool. This tool has a JavaScript button scanner that will scan your Salesforce org for JavaScript buttons, recommend some type of conversion for each button, and can, if you choose, automatically convert those buttons to their Lightning alternatives. Those alternatives can vary, from a simple button or link, to a quick action, to creating a Lightning component that will fully or partially replace your JavaScript button.

However, the converter tool can sometimes fail to convert some of your JavaScript buttons. A common reason, I have found, that the converter tool fails is because your JavaScript buttons execute one or more Apex classes. For simpler Apex calls, you can attempt converting your JavaScript buttons with a quick action that runs a Lightning flow. Flows are capable of running Apex code, so if you can configure your flow correctly to provide an Apex static method the correct inputs, you should be good to go.

Sometimes, the JavaScript button performs a redirect after running some Apex code. In that case, it is best to use Lightning components, running as a Lightning action, to execute the Apex code and perform the redirect. Both Lightning Web Components (LWCs) and Aura Components are capable for this use case, and LWCs are preferred because they are based on modern web standards.

I will not go into much detail about how to convert JavaScript buttons that run Apex code in this post, but you can either learn more about converting JavaScript buttons in the Trailhead module, [Lightning Alternatives to JavaScript Buttons][lex_javascript_button_migration]{:target="_blank"}, or you can stay tuned for the next post, where I go through a few examples of how to perform the conversion!

[lex_javascript_button_migration]: https://trailhead.salesforce.com/en/content/learn/modules/lex_javascript_button_migration
[lcc_javascript_buttons_scan]: https://developer.salesforce.com/blogs/2018/06/convert-javascript-buttons-to-lightning-friendly-alternatives-with-the-lightning-experience-configuration-converter.html
