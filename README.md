# mcquery-reset
Reset you Mailchimp for Salesforce (MC4SF) MC Query records so your Salesforce contacts synchronize with Mailchimp.
* Introduction
* Solutions


# Introduction
If you are using Mailchimp for Salesforce, you've likely noticed that sometimes Mailchimp for Salesforce is does not fully synchronize contacts.  You might have questions like:

* Why is Mailchimp for Salesforce unable to sync contacts?
* Why is Mailchimp for Salesforce no longer synchronizing contacts?
* Why has this Member Query stopped working? 

For example, I created a member query to tag Mailchimp contacts as "Donated".  In this way, when emailing appeals, we could could use a Mailchimp segment to fitler out contacts making donations. I configured the member query to run in the early morning.  When the organization starts a new appeal campaign, I clear the Donated tags in Mailchimp, and I update the member query (in Salesforce) with a new donated since comparison date. Unfortunately, I noticed that only a few of our Mailchimp contacts got tagged as Donated even though there were Salesforce contacts where last donated date was such that the contact should have been tagged.

I surmised that The Mailchimp for Salesforce member queries are stateful.  On the first run, a member query applies the filters to all the Lead, Contact, or Campaign Member objects/records for which ever object type is selected.  On subsequent runs, the member query checks the Salesforce objects/records that have changed since the last date and time the query was run. It maybe helpful to think of the current Mailchimp for Salesforce **Run Query** link as meaning **"Run the query BUT only apply it to records that have changed since the member query's last run date and time."**

**Changing the query's filter _does not_ reset the member query's statefullness.** Thus, if you are debugging or enhancing your member query you may find yourself thinking that it's not working after you use the Run Query link. However, if you recreate a new member query using the same logic, it will apply itself to all the approrpriate records.

The member query statefullness is provided by the MC Query object's **Last Run** field, type Date/Time.

Mailchimp support has suggested a work around of changing the Last Modfied date on all the records you want to process. You can do this using your favorite dataloader or by manually editing every record you want processed. In my opinion, this is a poor work around. You might have reports or other solutions that use the object's last modified date.

# Solutions

work-in-progress

1. Add a "Change Last Run Date" Quick Action where the Action Type is Update Record. 
* Advantages: Suitable for a novice Salesforce administrator.
* Disadvantages: Not one click for end user since they must clear the date fields manually.

2. Add a "Reset Last Run Date" Quick Action with a "Reset MC Query Last Run Date" flow.
* Advantages: One click for the end user.
* Disadvantages: Requires a Salesforce flow (but, it is really simple).

https://trailhead.salesforce.com/en/content/learn/modules/lex_javascript_button_migration/javascript_button_alternatives
You could probably improve this approach so that it's just a one click action.

3. Circumvent the Mailchimp for Salesforce out-of-the-box UI for the MC Query object so you have a standard edit form.  You can do this from the Object Manager. Select the MC Query object, select Buttons, Links, and Actions, and then override the Edit and View buttons to use the standard pages.

Of course, the better solution will be for Mailchimp to provide this funcationality as part of their installation package with some user friendly buttons or links.  For example, clicking on "Run Query" could prompt you with a dialog that asks you if you want to apply the query to all the objects or just the ones that changed since the last query run.
