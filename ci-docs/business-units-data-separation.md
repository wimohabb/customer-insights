---
title: Business unit support and role-based access control (preview)
description: Learn how business unit support and role-based access control regulate access to customer profiles, segments, and measures in Dataverse.
ms.date: 07/13/2023
ms.reviewer: mhart
ms.service: customer-insights
ms.topic: concept
author: jodahl
ms.author: jodahl
ms.custom: bap-template
---

# Business unit support and role-based access control (preview)

[!INCLUDE [consolidated-sku](./includes/consolidated-sku.md)]

Business unit support allows administrators to regulate access to customer profiles, segments, and measures based on business units. [Learn more about business units and role-based access control in Dataverse](/power-platform/admin/wp-security-cds).

## Prerequisites

- Business units and associated teams are defined in Dataverse. [Learn more about setting up business units in Dataverse.](/power-platform/admin/create-edit-business-units)
- Users are assigned to appropriate business units and teams. [Learn more about managing users and teams in Dataverse.](/power-platform/admin/users-settings)
- Teams used in Customer Insights must have the role *Customer Insights Data Read Access* assigned. [Learn more about assigning roles in Dataverse.](/power-platform/admin/manage-teams#manage-the-security-roles-of-a-team)
- Business unit data separation is enabled by an admin in **Settings** > **System** > **Business unit data separation**.
- All data sources that contribute to unification must have a column that holds a value that identifies the business unit for every row of data.
- A B-to-C (individual customers) Customer Insights environment.

> [!NOTE]
>
> - B-to-B (business accounts) environments are not supported.
> - Parent-child scope is currently not supported.
> - [Modernized business units](/power-platform/admin/wp-security-cds#matrix-data-access-structure-modernized-business-units) are not supported.
> - Field-level security is not supported.
> - **It is not possible to disable business unit data separation on an environment after it has been enabled.**

## Access controls in Customer Insights

### Customer profiles, activities, customer measures, intelligence, enrichments

Access to a customer profile in Customer Insights depends on the business unit team that owns the profile, the business unit team of the user, and the Customer Insights role of the user. The *Administrator*, *Contributor*, and *Viewer* roles have access to all profiles regardless of the owning business unit team. The *Marketing contributor* role has access only to customer profiles that belong to their business unit.

The *Marketing contributor* role only has access to the *customers*, *tables*, *segments*, and *measures* views in the Customer Insights user interface. The Marketing contributor role has a limited set of functionality. [Learn more about user roles in Customer Insights](permissions.md).

> [!NOTE]
>
> The **administrator** and **contributor** roles are highly privileged and should only be given to users that belong to the *Root* business unit.The **viewer** role is not bound by business unit and should not be used in production if business unit data separation is enabled.

Ownership of the customer profiles is determined based on mappings that are configured on the [Unify](data-unification.md) page:

1. Go to **Data** > **Unify** > **Business units**
2. Under **Business unit data separation**, select the column that identifies the business unit for each entity that contributes to unification. Unification rules can't be added on the selected columns.
3. Under **Associate customer profiles with business units**, specify the mapping between the values in the selected columns and business unit teams. Only teams that have the *Customer Insights Data Read Access* role are available for selection. For example, 'A' maps to the business unit A team and 'B' maps to the business unit B team.

![Screenshot of business unit mappings.](media/BU_mappings.png)
*Screenshot of business unit mapping.*

Teams within business units (not business units directly) own customer profiles to provide better control of data access. Only one team per business unit can be specified in the mapping rules.

- The system deduplicates and unifies profiles only if the business unit values match.
- Profiles that don't match any of the mappings are assigned to the *Org* business unit.
- All profiles are assigned to the *Org* business unit if business unit data separation isn't enabled.
- A profile belongs to exactly one business unit.
- The unification rules and customer profile schema are the same for all business units.

> [!NOTE]
> Any changes to the business unit data separation configuration triggers a full refresh. If your data sources use incremental updates, a [full refresh](incremental-refresh-data-sources.md#run-a-one-time-full-refresh-for-azure-data-lake-data-sources) needs to be triggered manually after changes have been made.

Data relates to a customer profile, for example activities, inherit the business unit ownership from the associated profile.

Intelligence models are trained on all data, regardless of business unit data separation.

## Default configurations

The following diagram shows a typical business unit structure. Marketing contributor users only have access to their business unit team's customer profiles. For example, if a marketing contributor user creates a segment with all customers, it only contains the customer profiles that map to the business unit of the segment owner.

![Example of a business unit structure with the Org parent business unit at the top and child business units A to D.](media/BU_structure_example_Root.png)
*Example of a business unit structure with the Org parent business unit at the top and child business units A to D.*

### Segments and business measures

Segments and measures belong to the business unit of the user that created them. For example, if a user is a member of business unit *A* then any segment and measure that user creates belongs to business unit *A*. Access settings in Dataverse control access on the segment and measure definitions tables in Dataverse. By default, segments and measures are available to other users that belong to the same business unit.

> [!NOTE]
>
> - Segments and measures are owned by only one business unit and cannot be shared with other business units.
> - Only *build your own* segments and measures and no projected attributes are supported for the marketing contributor role.
> - For building segments and measures the Marketing contributor role only has access to Customer profiles, Unified activities, Segments, and Customer measures.

## Next steps

- [Work with Customer Insights data in Microsoft Dataverse](customer-insights-dataverse.md)
- [Customer Insights business unit integration with Dynamics 365 applications and Power Platform (preview)](business-unit-integration-dynamics365-power-platform.md)

[!INCLUDE [footer-include](includes/footer-banner.md)]