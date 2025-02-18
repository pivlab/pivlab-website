---
title: Team
nav:
  order: 3
  tooltip: About our team
---

# {% include icon.html icon="fa-solid fa-users" %}Team

Our current lab members include postdocs, students, and staff.
We are passionate researchers specializing in various fields, yet we work together as a cohesive team.
We enjoy learning from each other and strive to make a significant impact on society.

{% include list.html data="members" component="portrait" filter="role == 'pi'" %}
{% include list.html data="members" component="portrait" filter="role == 'postdoc'" and group != 'alum'" %}
{% include list.html data="members" component="portrait" filter="role == 'phd'" and group != 'alum'" %}
{% include list.html data="members" component="portrait" filter="role == 'ms' and group != 'alum'" %}
{% include list.html data="members" component="portrait" filter="role == 'undergrad'" and group != 'alum'" %}
{% include list.html data="members" component="portrait" filter="role == 'programmer-lab'" and group != 'alum'" %}
{% include list.html data="members" component="portrait" filter="role == 'programmer'" and group != 'alum'" %}

{% include section.html dark=true %}

{:refdef: class="center" style="font-size: var(--xxl);"}
**We are hiring!**
{: refdef}

We're currently hiring for several positions.
**Join us** and become part of our team.
{:.center}

{%
  include button.html
  text="Join the lab"
  link="join"
  style="button"
%}

{% include section.html %}

## Alumni

We are deeply grateful to the past members of the lab, who have made significant contributions towards achieving the lab's goals.
They will be greatly missed!

{% include list.html data="members" component="portrait" filter="role == 'pi' and group == 'alum'" style="small" %}
{% include list.html data="members" component="portrait" filter="role == 'postdoc' and group == 'alum'" style="small" %}
{% include list.html data="members" component="portrait" filter="role == 'phd' and group == 'alum'" style="small" %}
{% include list.html data="members" component="portrait" filter="role == 'ms' and group == 'alum'" style="small" %}
{% include list.html data="members" component="portrait" filter="role == 'undergrad' and group == 'alum'" style="small" %}
{% include list.html data="members" component="portrait" filter="role == 'programmer-lab' and group == 'alum'" style="small" %}
{% include list.html data="members" component="portrait" filter="role == 'programmer' and group == 'alum'" style="small" %}

{% include section.html %}
