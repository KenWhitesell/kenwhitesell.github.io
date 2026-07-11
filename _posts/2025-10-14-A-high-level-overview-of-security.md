---
layout: post
title: A high-level overview of information security
tags: Django
---

## Why this post?

This is intended as a companion to my post on Django deployments, intended to explain some terms and ideas referenced there but not explained.

This post is focused on security issues related to the **deployment** of a Django app. Addressing security during the _development_ of a Django application is just as important, but is not my concern _here_.

### TL;DR

#### Security is important.
Threats to site security exist. If your site is accessible through the internet, you will be attacked.

#### Security is intentional.
Defaults are fine _if_ it's a conscious decision to use them.

#### Security covers a wide range of issues.
It's not just "preventing unauthorized access."

#### Security is site-specific, it is not "One size fits all".

#### Security is an on-going process.
Security is **not** a "One and done" step nor a simple product to be installed and forgotten.

> There is nothing "new" here - all the information below is available in multiple locations. Consider this a summary of what _I_ consider important. Others will have different opinions.
>

## "Secure" is not a boolean value

I frequently see questions asking some variation of "Is "x" secure?", where "x" might be a platform, library, or a section of code.

The answer is **always** one of:
- "It depends"
- "Yes, for _some_ definition of secure"
or
- "No, no system is ever fully secure"

### Security is a continuum

Some of the things that it depends upon include:
- What are the assets at risk?
- What is the potential loss?
- What is the environment in which it will be used?

The answer is neither "Yes, it's secure" nor "No, it's not secure". If you want to be accurate, it's "Yes, it's secure enough" or "No, it's not secure enough".

Think of a bicycle.

You generally wouldn't buy a $50 lock for a bicycle only worth $10. (Yes, there are exceptions.) In most situations, a $10 lock might even be considered too much for a $10 bicycle. At the other end, you might want to consider spending more than $50 on a lock for a $500 bicycle. You want to select the _right_ degree of protection for that asset.

Information security is the same. You want to invest the appropriate amount of effort to secure your assets.

This means that the decision regarding those efforts must be made not only in the context of the value of those assets but also in the risks that exist as well.

I have some projects that ran in isolated and air-gapped environments. The risks to those projects existed solely from the people using the equipment. That creates a much different set of security requirements than (for example) a system preparing ACH (electronic fund) transfers between banks.

## What do we mean by "Secure" anyway?

Many people think of security as simply preventing an unauthorized person from accessing data. And while that _is_ a _part_ of security, it's not the entire picture.

The model that I was taught 25+ years ago, and continue to use, is that Information Security is concerned with preserving four characteristics of a system:
- Confidentiality
- Integrity
- Availability
- Privacy

> Side note: This is the model that _I_ use. Many well-known published sources refer to the first three as the "CIA Triad", and are considered the "fundamental" characteristics. Other people have added additional characteristics to this list. If you search around the internet, you can find many other definitions as well. All are valid when discussing this topic.

Being aware of these different characteristics helps you to understand why some steps are taken to improve security. Many people tend to only think about confidentiality and ignore the other three as not being security-related.

### Confidentiality

This represents the organization's concerns regarding the distribution of information. Data should only be available to those individuals having the appropriate need to access it.

This may be something as simple as some data only being accessible to authenticated individuals, or as complex as to restrict access by time-of-day or other arbitrary criteria.

### Integrity

You want the data to be "right". You don't want unauthorized people changing data. And, you may also want to put safeguards around particularly sensitive data to help avoid mistakes or malfeasance. (E.g. You might want to require multiple approvals for payments exceeding some amount.)

### Availability

The joke used to be that the only truly secure system is one that's unplugged, disassembled, encased in lava, and then sunk to the bottom of the ocean. However, such a system fails the "availability" test.

Being secure means taking the steps necessary to ensure the system is up whenever it's needed. Your system could be so critical that there needs to be multiple levels of redundancy across multiple locations - think of the internet itself as one example. Or, to the other extreme, it may be like this blog, and have so little importance that its absence wouldn't really be missed.

However, simply being "up" or "down" doesn't completely cover the situation either. If your system isn't fast enough - if the users can't access the data because timeout thresholds are being hit or that it's taking too long to get anything done productively, that can also be considered a security-related issue.

### Privacy

This is different from confidentality in that it addresses an individual's concerns regarding the distribution of information about them. It may apply to customers, clients, consumers, or other people who are the subject of that information. Many requirements in this area are governed by laws such as [HIPAA](https://www.hhs.gov/hipaa/index.html) and [GDPR](https://gdpr-info.eu/).)

## Common principles and practices

There are some common security principles and practices that are fundamental to system security. Some of these even pre-date the computer age where the manual processes implementing them goes back more than 100 years. As business principles, they're just as valid now as they were then. The [OWASP Developer Guide](https://devguide.owasp.org/en/02-foundations/03-security-principles/) identifies 16 of these, but I believe that there are six that are most important when looking at the deployment of a web application.

- Security by Design
- Defense in Depth
- Fail Safe
- Least Privilege
- Separation of Duties
- Secure the Weakest Link

### Security by Design

Design your deployment environment with security in mind. Start with identifying the assets involved and the risks to those assets. Think about what you would lose if each of the characteristics are compromised.

- What is the cost of altered data?
- Is there potential embarassment (or worse) if the site gets defaced?
- How much revenue might you lose during an interruption of service?
- How much trust will your customers lose if there's a data breach?
- Does a competitor gain an advantage by data being exposed?

You will want to calculate the potential loss - assign some type of monetary value to it, and then figure out the cost of preventing that loss.

Then once you've identified the risks, you can start to identify which protective measures to employ.

For example, if you're running an ecommerce site generating more than $100,000 per day of revenue (call it $50 million annually), it's probably worthwhile to have multiple replications of your site in multiple geographic locations. This puts it into a much different category than my personal hobby sites that don't generate any revenue at all.

While the costs involved in a more secure deployment are not negligible and should not be ignored, you are generally better off if you over-protect rather than under-protect. My hobby sites don't generate revenue but do consume my personal time and energy. I choose to secure my sites to avoid having to rebuild and recover them due to malicious activity.

I consider this principle to be the most important. It is significantly more difficult to migrate a less-secure deployment process to a more-secure environment than it is to implement the more-secure environment in the first place.

### Defense in Depth

Don't assume that any one security control is always going to work perfectly and will never be compromised. Always consider the worst-case scenario of "What happens when component "X" fails?"

This involves looking at every component individually, and considering the implications.

For example, assume that for whatever reason, nginx gets compromised such that an individual can request any file from any location in the file system. (This could happen either by a bad configuration or an unidentified flaw in the code - the reason is not important _here_.) In the "worse-case" evaluation, this means that the person could request things like settings or configuration files containing passwords or other critical data. From there, that information could be used to focus attacks on other critical components.

One way to mitigate the risk associated with this is to ensure that the account running nginx ("www-data" by default on Ubuntu) can _only_ access those directories containing non-critical information.

### Fail Safe

If a failure occurs, you want the system to enter a most-secure state.

In the simple case, this means not exposing internal information to clients when a system error occurs.

It's this principle that covers one of the reasons why you never want to deploy a Django project with `DEBUG=True`. Too much information is provided to the browser on 404 or 500 errors.

But the implications of this principle go a lot farther than that when deploying you project. You need to consider the possible failure states of all components. What happens when the volume for your log files fills? What happens if there's a runaway process that consumes all available memory? What happens if someone tries to upload a 1 TB file?

These are all samples of conditions that need to be considered, among others. Again, how you respond to these depends upon your specific requirements.

### Least Privilege

Most Django developers are used to handling this within their application, identifying roles or users with specific permissions.

When you're looking at a deployment environment, you're looking to manage multiple types of resources. It not only the access to data, but you may also need to manage access to tcp ports, domain socket files, data volumes, and other system resources. The idea here again is to limit the damage that can occur if a component is compromized.

Frequently then, applying this principle in practice means running separate processes as separate accounts, each having the least permissions possible for the operations that that process is responsible to perform. For example, the database account you use to run migrations does not need to be the same account you use when running your site. The account used for migrations needs ALTER permissions on the database, while those operations are not usually performed during normal operations. By using an account without ALTER permissions on the database for normal operations, you prevent a wide range of possible attacks.

### Separation of Duties

This principle goes beyond technical issues to address some personnel or administrative processes.

You may be working in an environment where you have designated database administrators who have the responsibility of managing access to data. It's their job to create users and passwords, and that the passwords to production data are strictly controlled.

You might also have a server management team responsible for the configuration and operations of the servers with their own tools for deployment.

You might be working within an "elevation process" where your project gets deployed to different testing and staging environments prior to being released to production. There may also be multiple levels of review required before those deployments can proceed.

In any of these cases, your deployment process would need to work within these types of frameworks. How you structure your project may need to be adjusted to account for this. You may also find it necessary to create specific documentation to address how these different roles perform their required duties.

### Secure the Weakest Link

Your site is only as secure as the most vulnerable component.

This is a factor that needs to be reviewed periodically! You cannot perform this evaluation one time when you deploy and then forget about it after that.

Exploits and potential vulnerabilities are constantly being identified. Depending upon your degree of involvement with the deployment process, you must be willing to invest the necessary time and effort to remediate issues that have been found.

Ongoing security efforts should be focused on identifying vulnerable components, and the options available to you to make them more secure.

In many cases, the solution may be to upgrade to the most recent version of a component. This does have some degree of risk in that the upgrade may change some characteristic you are depending upon.

In some cases that may even involve changing components if the degree of risk is unacceptable.

## Security By Obscurity is **not** secure, except... !

You will frequently see authors write that "Security by obscurity is not security." Technically, it's true. However, I believe that it's mostly applicable to those situations where a site is being specifically targeted.

For most sites, this isn't really a concern. Your typical individual or small-business site does not have sufficient value to justify the effort of a sustained and directed attack.

The vast majority of web sites are threatened by scripts being run against whatever those scripts can find. Most of them are looking for well-known vulnerabilities in common systems such as Wordpress, Shopify, and Drupal.

For example, a very small sampling of the access logs for my site shows a list of requests with urls like:
```
"GET /api/sonicos/tfa HTTP/1.1"
"GET /api/v1/pods HTTP/1.1"
"GET /app/config/parameters.yml HTTP/1.1"
"GET /app_dev.php/_profiler/open?file=app/config/parameters.yml HTTP/1.1"
"GET /boaform/admin/formLogin?username=user&psd=user HTTP/1.0"
"GET /cms/wp-includes/wlwmanifest.xml HTTP/1.1"
"GET /wp-content/themes/seotheme/db.php?u HTTP/1.1"
"GET /wp-includes/wlwmanifest.xml HTTP/1.1"
```

None of these are valid on my system, but that doesn't prevent me from getting thousands of requests like this every day. On average, more than 90% of all requests to my site each day are attacks - and that's _with_ using `fail2ban` to block repeated attempts. At any point in time I typically have about 1000 blocked IP addresses along with 3 complete `/16` subnets.

The lesson I take from this is that there _is_ value in changing certain defaults to reduce the probability of a random scan finding something worth trying to exploit.

For Django, this means that the Django admin _never_ resides at `/admin`. It is always moved to a different path, and the path varies by project. It also generally means that I don't have any urls based on `/` (the root path). Every valid path is in some location below root. This makes it easier to use nginx to filter out the vast majority of these invalid requests.

In some cases, this also means that you might want to have `STATIC_URL` and `MEDIA_URL` use non-standard or atypical URLs. Using paths other than `/static` or `/media` will have some benefit.

## Final thoughts

All decisions regaring the security posture of your site should be intentional.

It's ok to accept the defaults if it's a conscious decision.

If you've evaluated the risks to your system and have decided that a given configuration yields an acceptable degree of risk, then it's fine to move forward with that configuration. At a minimum, make some notes to identify the decision made, and why.

You're usually a lot better off being in the position of "We looked at that and thought we would be ok" than "We didn't think of that".
