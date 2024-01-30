---
title: Linked Audiences
plan: unify
beta: true
---

> info "Linked Audiences is in private beta"
> Linked Audiences is in private beta, and Segment is actively working on this feature. Some functionality may change before it becomes generally available. [Contact Segment](https://segment.com/help/contact/){:target="_blank"} with any feedback or questions.

With Linked Audiences, you can use the relational data you've defined in your Data Graph to build audiences and send them to your downstream destinations. 

From the relationships you've defined between the profiles and entities in your warehouse, you can filter on an entity, profile trait, and audience membership conditions to create hyper-segmented audiences.

> info "Linked Audiences warehouse support"
> At this time, Linked Audiences only supports Snowflake. 

> success ""
> Before you build Linked Audiences, be sure you've defined entities in your [Data Graph](/docs/unify/linked-profiles/data-graph/). 


## Getting started

To help you get started with Linked Audiences, consider the following best practices: 

1. It may be helpful to first identify an entity that is directly associated with your users, such as `Accounts`, `Households`, or `Organizations`.
2. From there you can define relationships between this entity with any of the other entities that may be associated with it, such as how `Accounts` can have the following: `Subscriptions`, `Purchases`, or `Carts`. 
3. You can create entity relationships up to six levels in depth. For example, an entity condition that queries for relationships between `Profiles`, `Accounts`, `Credit Cards`, and `Transactions` has four levels of depth. 
4. To further refine your audience, identify column values that you might also want to filter your entities by, or configure profile trait and audience membership conditions. 

## Use cases
 
Below are some example use cases to help you learn more about Linked Audiences.

### Build an audience of cat owners who are also a part of the platinum membership tier

Build an audience with `Households` and `Pets` where:
- `pets.type` = "cat"

And where:
- `profile.audience_membership` = "Platinum membership tier"

In the Data Graph, `Households` and `Pets` are defined as entities and are represented as separate tables in your data warehouse. 

Relationships are defined between:
 - `Profiles` and `Households` 
 - `Households` and `Pets` 

In the warehouse, `pets.type` is a column in the `pets` table. By filtering against the `pets.type` column for the "cat" value, marketers can return a list of users that have a cat. 

Furthermore, filtering the audience against `profile.audience_membership` for "Platinum membership tier" will allow marketers to further refine their audience to only include users who are also a part of the platinum membership tier audience.

### Build an audience of users with premium subscriptions who are located in Canada

Build an audience with `Accounts` and `Subscriptions`, where the following are true:
- `subscription.status` = "active"
- `subscription.tier` = "premium"

And where:
- `profile.country` = "Canada"

In the Data Graph, `Accounts` and `Subscriptions` are defined as entities. Relationships are defined between:
- `Profiles` and `Accounts`
- `Accounts` and `Subscriptions` 

In the warehouse, `subscription.status` is a column in the `subscriptions` table. Marketers can refine their audience by filtering against the `subscription.status` and `subscription.tier` columns to return a list of users that have an active subscription to their premium offering.

Furthermore, filtering the audience against `profile.country` for "Canada" will allow marketers to further refine their audience to only include users who are located in Canada.


### Build an audience of credit card holders with a certain number of transactions

Build an audience with `Accounts`, `Credit Cards`, and `Transactions` where the following are true: 
- `credit_cards.name` equals "Owly Card" 
- `transactions.count` is greater than five

In the Data Graph, `Accounts`, `Credit Cards`, and `Transactions` are defined as entities. Relationships are defined between:
- `Profiles` and `Accounts`
- `Accounts` and `Credit Cards`
- `Credit Cards` and `Transactions`

In the warehouse: 
- `credit_cards.name` is a column in the `credit_cards` table
- `transactions.count` is a column in the `transactions` table

Marketers can create hyper-targeted user segmentations filtering by column values or attributes, such as "Owly Card" and integers. 


## Step 1: Build a Linked Audience

Use the Audience overview page to build or maintain a Linked Audiences.

1. Navigate to **Engage > Audiences**.
2. Click **+ New audience**, then select **Audience**.
3. On the Select Type screen, select **Linked audience**, then click **Next**.
4. Build your Linked Audience with profiles by selecting **associated with an entity**, **where profile trait**, or **part of an audience** conditions.
5. Preview your audience, then click **Next**.
6. Enter an audience name and description, then click **Create Audience**.

After creating the audience, you'll be redirected to the Overview page. By default, the audience is disabled. You'll need to manually enable the audience for the audience to compute.

> warning ""
> Linked Audiences can't be edited or deleted. Create a new audience to update conditions. To disable an audience, navigate to the **Settings** tab of an audience and toggle the **Enabled** button off.


## Step 2: Activate your Linked Audience

You can use your Linked Audience to activate any [actions-based destination](/docs/connections/destinations/actions/#available-actions-based-destinations). The steps below provide instructions on how to add a destination to your Linked Audience(s), and send an event that best matches your use case.

> info ""
> Segment has preset mappings for [Braze](/docs/connections/destinations/catalog/braze-cloud-mode-actions/#available-presets), [Iterable](/docs/connections/destinations/catalog/actions-iterable/#available-presets), and [Customer.io](/docs/connections/destinations/catalog/customer-io-actions/#available-presets) with many of the following steps already configured.

> warning ""
> Some action destinations have limitations on nested objects, and the depth of the `_entity_context` property.

### Step 2a: Add an action destination

To activate your Linked Audience, you first need to add an action destination.

From the Add destination window, select your destination and click **Next**.

### Step 2b: Select event 
After adding an actions-based destination, select what type of event you want to send to the destination. Events update the destination about changes to your entity or audiences. You can then use these actions to orchestrate campaigns in other tools.

You can send events:
- When an entity on a profile changes:
    - [Entity Added](#entity-added)
    - [Entity Removed](#entity-removed)
- Based on profile audience membership updates:
    - [Audience Entered](#audience-entered)
    - [Audience Membership Changed](#audience-membership-changed)
    - [Audience Exited](#audience-exited)

> info ""
> You can't send events before you identify people. Ensure you're making the profile(s) known in the destination before you send events. You can do this by sending an `Audience Membership Changed` event first, or by creating an Identify event in Connections.

#### Entity added

Send a Track event when an entity matching the audience conditions is added. Select your entity from the **Select entity** dropdown menu.

Example use cases:
- Send a reminder to a customer when a credit card associated with their profile has an outstanding balance.
- Notify a traveler when a flight associated with their profile is delayed.
- Notify a customer when a product associated with their profile's wishlist is back in stock.

#### Entity removed

Send a Track event when an entity is removed because it no longer matches the audience conditions. Select your entity from the **Select entity** dropdown menu.

Example use cases:
- Send a confirmation to a customer when a credit card associated with their profile has been paid off.
- Send a confirmation to the primary doctor when each of their associated patients completes their annual check up.

#### Audience entered 

Send an event when a profile enters the audience.

Example use cases:

- Send a congratulatory email when a travel qualifies for premium status.
- Send a discount to all customers with a particular product on their wishlist.

#### Audience exited

Send an event when the profile exits the audience. 

Example use cases:

- Send an email to credit card owners to confirm that their credit cards have been paid in full.
- Send a confirmation to a patient when they have completed all their pre-screening forms.

#### Audience Membership Changed

Send events when a profile enters and exits the audience.

Example use case:
- Update a user profile in a destination with the most recent audience membership.


### Step 2c: Select an action

Select the destination action to call when the event happens.

Segment displays available actions based on the destination action you've connected with your Linked Audience. 

Visit the [destination actions docs](/docs/connections/destinations/actions/) learn more about destination actions, and view available actions for your destination.

### Step 2d: Configure the event

Configure your event and select additional properties to include in the event. 

To configure your event:

1. Select additional properties to include in each event. 
- As you're configuring your event, you can view a preview of the enriched event based on your property selections. 
2. Map your event from your audience to your destination.
- You can preview what the event will look like in your destination.
- [Braze](/docs/connections/destinations/catalog/braze-cloud-mode-actions/#available-presets), [Iterable](/docs/connections/destinations/catalog/actions-iterable/#available-presets), and [Customer.io](/docs/connections/destinations/catalog/customer-io-actions/#available-presets) are pre-set destinations, so you won't need to map your audience to your destination.
3. Select if you'd like to [send events for current profiles and entities in the audience](#send-events-for-current-profiles-and-entities-in-the-audience).
- This is only available for the **entity added**, **audience entered**, and **audience membership changed** event types.
4. After configuring, click **Save** to enable your Linked Audience. 


#### Send events for current profiles and entities in the audience

Select if you'd like to sync existing profiles and entities in the audience with the **Send events for current profiles and entities in audience** checkbox.

This box is unchecked by default. If you leave this box unchecked, Segment only sends events for new profiles and entities that match the audience conditions.

However, if you select this checkbox, Segment will also send events for profiles and entities that currently match the audience description. 

## Step 3: Confirm the payload in your destination

Linked Audiences sends events to your destination after Segment computes the audience. 

To confirm your destination is receiving events, Segment recommends that you log in to your destination and perform one of the following:
- Monitor the event activity
- Search for the `UserID` or `Event Name` (for example, `Entity Added`)



