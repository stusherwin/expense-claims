# Expense Claims System

## User registration and approval

The following diagram illustrates the different user interactions during the registration process:

![Register and verify users](/spec/register-approve.png)

When a new user accesses the system they will see a welcome screen which prompts them to register:

![Welcome](/spec/mockups/01%20Welcome.png)

Clicking on `Register` will take them to the registration screen:

![Register](/spec/mockups/02%20Register.png)

Once they have entered their details, they will be shown a confirmation message, and will be sent an email with a link for them to verify their email address:

![Registration complete](/spec/mockups/03%20Registration%20complete.png)

Once they have verified their email address, an email will be sent to all Catalyst members informing them a new user has registered:

![Verify email address](/spec/mockups/04%20Verify%20email%20address.png)

When a Catalyst member clicks on the link in their email, they will be taken to the user approval screen:

![Approve user registration (Catalyst only)](/spec/mockups/05%20Approve%20user%20registration%20(Catalyst%20only).png)

Once approved, the new user will be sent an email, with a link which will allow them to immediately access the system and make a new claim.

### Notes/Questions

1. If the user doesn't verify their email address, Catalyst will never be notifed that they have registered. The system should send the user a reminder email if they haven't verified after a period of time
1. What happens if Catalyst doesn't approve a user registration? Should there be a way to inform the user that they need to change their details before they can be approved?
1. Users will need to be added and removed from co-ops and working groups after their initial registration. Should a user be able to update their own co-op and working group membership or should only Catalyst be able to do this? If the user is allowed to do this then presumably Catalyst would also need to approve these changes before they take effect.
1. Users should be able to change their name, email address and bank details, presumably Catalyst would not need to approve these changes.
1. If a user changes their email address they will need to go through the process of verifying their new email address
1. Bank account details are easy to mis-type, there should probably be some validation on these fields to confirm the account numbers. This is usually done by calculating a checksum in the account number/sort code, but this may be time-consuming to implement or require some third party plugin.

## User login
So many web applications require users to create a username and password that they have to remember. This just leads to users either 

  * forgetting their passwords and having to use the 'Forgotten password' feature, which resets their password via email, or 
  * using the same password for every application, which is insecure.

An alternative is to email itself as the authentication for the expense claims system. This is no less secure than the 'forgotten password' feature of most websites, which effectively uses the user's email address as the means of authentication.

Every time the user wants to use the system, they request a login link which is emailed to them. Once the user clicks this link they will be able to access the system for a period of time, after which their session will expire and they will have to request a new login link. There are several reasons for why this method is a good fit for this app:

  * The user already needs to be online to use the system, and is likely to be able to access their emails at the same time
  * The system is likely to be used fairly infrequently, and so there's a high likelihood of the user forgetting their password
  * Most users' primary interaction will be through responding to emails sent to them via the app anyway

The user will be able to manually log out of the system, and once logged out, if they try to access the system again they will have to request a new login link. Once a user is logged out or their session has expired, on attempting to access the system again they will see a welcome screen:

![User login](/spec/mockups/06%20User%20login.png)

When they click on the link in the email, they will be automatically logged in and will be taken to their home page where they can see all their claims and any claims they can authorise or process, and start a new expense claim:

![User home screen](/spec/mockups/07%20User%20home%20screen.png)

### Notes/Questions
* The length of a session needs to be decided - the length of time a user can access the system without having to log in again. One day (24 hours) seems reasonable, although this could be extended if a longer period of time makes more sense.
* The links that are emailed to users should themselves have an expiry period.

## Making and authorising expense claims
The following diagram gives an overview of the different user interactions involved in making and authorising expense claims:

![Make and authorise claims](/spec/make-authorise-claim.png)

Users can make a claim by clicking the `New claim` button on their homepage, which will show the following screen:

![Make claim](/spec/mockups/08%20Make%20claim.png)

The expense claim must be against only one working group, and must include a list of itemised expenses, and uploaded supporting documents. Each expense item must be linked to one supporting document, but multiple expense items could link to the same supporting document (e.g. a single invoice could list multiple items)

All the documents will be uploaded to the server in a logical structure so that Catalyst can find them and review them outside of the expense claims system.

Once the claim is submitted, the working group core members will be sent an email notification:

![Claim submitted](/spec/mockups/09%20Claim%20submitted.png)

When a core member clicks on the link in their email, they will be taken to the following screen, where they will be able to review (but not edit) the claim, and write messages in the chat, which will be emailed to the person making the claim:

![Authorise claim](/spec/mockups/10%20Authorise%20claim.png)

 The person who made the claim will be able to respond to these messages and make changes to their claim, upload new supporting documents etc:

![Revise and re-submit claim](/spec/mockups/12%20Revise%20and%20re-submit%20claim.png)

At any point a core member of the working group may decide to authorise the claim, at which point an email will be sent to Catalyst members telling them that a new claim is ready to be processed, and the claimant will be emailed to say their claim has been authorised:

![Claim authorised](/spec/mockups/11%20Claim%20authorised.png)

When Catalyst click on the link in their email, they will be able to view the claim and mark it as processed once it has been paid:

![Process expense claim (Catalyst only)](/spec/mockups/13%20Process%20expense%20claim%20(Catalyst%20only).png)

When they mark it as processed, all parties will be notified by email that this has been done:

![Claim processed](/spec/mockups/14%20Claim%20processed.png)

### Permissions
* Any user can make an expense claim
* Only core members of the working group a claim was made against will be able to authorise a claim
* A user will not be able to authorise their own claim, even if they are a core member of that working group
* A user will not be able to authorise the claim of another member of the same co-op, even if they are a core member of the working group
* Catalyst members will be able to make claims, and authorise them as long as the above restrictions also apply
* Catalyst members will however be able to mark as processed their own claims or claims of other members of the same co-op. This is because marking a claim as processed is just an administrative activity.

### Notes/Questions

1. Proposed file system structure for uploaded documents: `Expense claims/{working group}/{claim id}-{date}-{member name}/{document id}-{document name}.{extension}`
1. Any changes the user makes to the uploaded documents should be reflected in the documents on the server (i.e. if a document is deleted in the app it should be deleted on the server etc.)
1. Should all members of a working group receive an email notification when a new claim is submitted, or just core members?
1. The system should work out what a user is allowed to do, and only present those actions to the user. For example if a Catalyst member logs in and views a claim from a working group they are a core member of, they should be able to authorise it, and then immediately mark it as processed.
1. The system should send reminder emails at periodic intervals to the relevant parties if a claim is still awaiting authorisation or processing.

## Closing an expense claim
![Closing a claim](/spec/close-claim.png)

Expense claims can become out of date, or may reach a deadlock in which no-one is willing to authorise them. In this case the original claimant, a core member of the working group, or a member of Catalyst will be able to close the claim using the `Close claim` button:

![Close claim](/spec/mockups/22%20Close%20claim.png)

When a claim is closed an email will be sent to all participants to notify them.

### Notes/Questions

1. Should a Catalyst member be able to close a claim that has been authorised? Presumably so if the payment can't be made for some reason (e.g. invalid bank details and the claimant can't be contacted)

## Catalyst-only actions
Catalyst members will be able to access more areas of the system than other users. They can:

* View all claims
* Create a new claim on behalf of another user
* Edit another user's expense claim and upload documents
* View and edit all users, and create new ones
* View and edit co-ops, and create new ones
* View and edit working groups, and create new ones
* Archive users, co-ops and working groups

### Viewing all claims
Catalyst members will be able to view all expense claims

![All claims (Catalyst only)](/spec/mockups/21%20All%20claims%20(Catalyst%20only).png)

### Making and editing claims
Catalyst members have the ability to create expense claims on the behalf of any other user, so they will have an extra section on the `Make an expense claim` screen:

![Make claim (Catalyst only)](/spec/mockups/23%20Make%20claim%20(Catalyst%20only).png)

Catalyst members will also be able to edit any unauthorised claim and upload supporting documents. Whenever a claim is altered in this way, the claimant and all working group core members will receive an email notification.

### Viewing all users
Catalyst members will be able to view all users and archive them. Archived users will no longer be able to log into the system, but they will still be shown on historical claims.

![Users (Catalyst only)](/spec/mockups/15%20Users%20(Catalyst%20only).png)

### Creating/editing a user
Catalyst members will be able to create new users, and edit any other user's details - in which case the user should receive an email notification that their details have been edited.

![Edit user (Catalyst only)](/spec/mockups/16%20Edit%20user%20(Catalyst%20only).png)

### Viewing all co-ops and archiving
Catalyst members will be able to view all co-ops and archive them. 

Users will still remain members of archived co-ops until they are manually removed, but new users will not be able to select an archived co-op when they register. 

The restriction that a user cannot authorise a claim from someone in the same co-op will immediately be lifted for that co-op when it is archived.

![Co-ops (Catalyst only)](/spec/mockups/17%20Co-ops%20(Catalyst%20only).png)

### Creating/editing a co-op
Catalyst members will be able to create new co-ops and edit existing ones, including which users are a member of the co-op. 

If the membership of a co-op is changed this will immediately affect whether those users will be able to authorise the claims of the other users in the co-op.

![Create_edit co-op (Catalyst only)](/spec/mockups/18%20Create_edit%20co-op%20(Catalyst%20only).png)

### Viewing all working groups
Catalyst members will be able to view all working groups and archive them.

Users will still remain members of archived working groups until they are manually removed, but new users will not be able to select an archived working group when they register, and an archived working group cannot be selected when making a new claim.

![Working groups (Catalyst only)](/spec/mockups/19%20Working%20groups%20(Catalyst%20only).png)

### Creating/editing a working group
Catalyst members will be able to create new working groups and edit existing ones, including which users are a member of the working group. 

If the core membership of a working group is changed this will immediately affect whether those users will be able to authorise the claims for that working group. Any new core members of the working group will be sent an email notification about all claims in that working group that are awaiting authorisation.

![Create_edit working group (Catalyst only)](/spec/mockups/20%20Create_edit%20working%20group%20(Catalyst%20only).png)

## Estimates
|Task|Time|
|-|-|
|Initial setup|? days|
|User registration|? days|

### Total: ? days