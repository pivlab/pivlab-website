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

{% include list.html data="members" component="portrait" filters="role: pi, group: " %}
{% include list.html data="members" component="portrait" filters="role: postdoc, group: " %}
{% include list.html data="members" component="portrait" filters="role: phd, group: " %}
{% include list.html data="members" component="portrait" filters="role: ms, group: " %}
{% include list.html data="members" component="portrait" filters="role: undergrad, group: " %}
{% include list.html data="members" component="portrait" filters="role: programmer, group: " %}

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

{% include list.html data="members" component="portrait" filters="role: pi, group: alum" style="small" %}
{% include list.html data="members" component="portrait" filters="role: postdoc, group: alum" style="small" %}
{% include list.html data="members" component="portrait" filters="role: phd, group: alum" style="small" %}
{% include list.html data="members" component="portrait" filters="role: ms, group: alum" style="small" %}
{% include list.html data="members" component="portrait" filters="role: undergrad, group: alum" style="small" %}
{% include list.html data="members" component="portrait" filters="role: programmer, group: alum" style="small" %}

{% include section.html %}
