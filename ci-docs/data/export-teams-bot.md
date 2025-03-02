---
title: "Teams bot for Dynamics 365 Customer Insights - Data (preview)"
description: "Look up unified customer profiles in Microsoft Teams with the help of a bot."
ms.date: 09/01/2023
ms.reviewer: mhart
ms.topic: how-to
author: pkieffer
ms.author: philk
---

# Teams bot for Dynamics 365 Customer Insights - Data (preview)

[!INCLUDE [public-preview-banner](includes/public-preview-banner.md)]

[!INCLUDE [consolidated-sku](./includes/consolidated-sku.md)]

Connect with Microsoft Teams to let a bot look up unified customer profiles in Teams channels.

:::image type="content" source="media/teams-bot.png" alt-text="Teams bot showing a customer record":::

## Prerequisites

- At least one [data source added](data-sources.md).
- The [unification process](data-unification.md) is complete.
- Fields are added to the [search & filter index](search-filter-index.md).
- Customer Insights - Data and Teams are in the same organization.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWRElj]

## Configure the bot

1. Go to **Settings** > **Connections**.
1. On the Microsoft Teams tile, select **Set up**.
1. You're redirected to the **Apps** area in Teams. You can also open Teams and select **Apps** in the bottom-left corner or [get it from AppSource](https://go.microsoft.com/fwlink/?linkid=2124104) directly.
1. Search for **Customer Insights** and select the app.
1. Select **Add**.
1. Sign in to Customer Insights - Data in Teams. A welcome message displays.

## Things you can do with the bot

The bot provides lookup capabilities for unified customer profiles.

- Enter **search** followed by a name, email address, or any other field on the unified customer profile that is added to the search & filter index.

  You'll get a card with up to 15 fields from the resulting customer profile. Multiple matches return a list of results where you can select a profile. To look up an exact match, add the search term in double quotes.

- If your organization maintains multiple Customer Insights - Data environments in the same organization, enter **switchinstance** to choose which environment you want to connect the bot to.

- Enter **help** to see a list of available commands for the bot.  

[!INCLUDE [footer-include](includes/footer-banner.md)]