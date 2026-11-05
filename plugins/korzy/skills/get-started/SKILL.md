---
name: get-started
description: Help a new user connect Korzy, check that it works and look up a first parcel. Use when the user has just installed Korzy, asks how to set it up or what it can do, or Korzy tools return a sign-in or subscription error.
---

# Get started with Korzy

Korzy is parcel research for the United States: find a parcel by address or coordinates, read its record and check what's nearby (water and sewer service, environmental layers, transport and data center projects).

## Connect

1. The first time Korzy is used, the client opens a Korzy sign-in. The user signs in, or creates an account at https://app.korzy.co/auth/signup; there is no key to paste.
2. Korzy needs a paid plan; there is no free trial. After signing up, the user picks a plan (https://korzy.co/pricing/#plans) and pays with Stripe Checkout, where they can enter a promotion code.
3. To confirm the connection, call `list_datasets`. It is free and lists datasets with coverage and dates. On a sign-in error, ask the user to reconnect Korzy in their client's connector or plugin settings.

## A first lookup

Ask the user for an address or a point they care about, or suggest one of these:

- "Look up the parcel at 714 Mordecai Towne Pl, Raleigh, NC."
- "What parcel is at 35.7999, -78.6270, and what's nearby?"
- "Who owns the parcel at this address?" (owner fields cost double, so fetch them only when asked)

Then follow the `korzy` skill for the parcel workflow.

## When something fails

- **401:** the connection needs signing in again.
- **402 `subscription_required`:** the account has no active plan; point the user to https://app.korzy.co/account to choose one.
- **403:** the key lacks access, or the request asks for an area or list search, which isn't available; look up by address, point or record ID.
- **429:** the rate limit or monthly credits ran out. Tell the user when credits reset and that they can add credits on their account page; don't retry repeatedly.
- **503:** temporarily unavailable; try again later and say so rather than guessing.

Never invent records or values when Korzy can't answer. For help, users can contact Korzy at https://korzy.co/contact/.
