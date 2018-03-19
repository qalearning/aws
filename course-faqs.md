# AWS Course Frequently Asked Questions
## 1 - Student materials

### Do I use VitalSource, Bookshelf or eVantage?
Any or all of them. They're all synonyms for the same thing. If you create an account on eVantage, you'll still be able to log in to VitalSource and *vice versa*. The company that distributes these materials is called Gilmore and they run a number of brands. Gilmore is a well-respected eBook delivery platform in the academic space.

### Can I access the materials after the course ends?
Yes. Your license code for the Student and Lab Guides is valid for 2 years online. If you install the offline reader and download your books, you will have perpetual access.

### How can I get the offline reader?
From the web UI: up in the top right corner of the page there is an icon that looks like nine squares. You'll be able to install the offline reader ("Desktop App") from there.

From the web: search for "bookshelf desktop app".

The offline reader enables you to have up to 4 devices authorised at a time - two desktop and two mobile. You can deactivate a device at any time by using the Web UI.

### Will I get updates to the materials?
Probably. Your license is for version *M.x* of the materials, where *M* is the major version number and *x* is any minor version number. If AWS Training & Certification release a new minor version of the course, you will be able to "update your book list" and get the new version. If they release a new major version of the course, you won't get the update.

### Can I get a copy of the instructor's slides?
Sorry, no, we are not permitted to distribute the slides. However, you don't need a copy of the slides; the Student Guide is generated from the instructor slide decks, so they are one and the same thing.

### Can I get a hardcopy of the guide?
Yes. If you install the offline reader and you have a PDF printer installed, you can print the guides out. There used to be a restriction of 10 pages at a time, but that restriction has been lifted. Your printed copy will be watermarked with your email address.

### I don't like this weird eBook format. Can I convert it to PDF?
Yes. If you install the offline reader and you have a PDF printer installed, you can print the guides out to PDF. The process can be a little hit-and-miss, depending on your O/S and PDF print driver. Your PDF will be watermarked with your email address. NOTE: the print to PDF can take quite a while depending on the size of the guide.

### Can I buy a hardcopy of the guide?
Yes. *Possibly*. The email containing your license code *may* also contain a link to purchase a hardcopy of the guide.

The cost varies - around 30 USD and items are shipped from the US at a cost of around 60 USD. You can only buy a hardcopy by following the link in the email, so if you really want to go down this route, depending on how you are attending the course, it may involve a bit of effort to forward you the license code email:

- If you are attending from anywhere, we will have already forwarded you the email.
- If you are attending in one of our public training centres, we will have printed out the email but should be able to track it down and forward it.
- If you are attending on your own premises then your trainer will probably have been forwarded a list of just the license codes without the appropriate hyperlinks, making it harder to track down the original email(s).

### Can I annotate the materials?
Yes. You can highlight text using your mouse and add notes to your highlights. These notes will be synchronised across all of your devices.

### What's the URL again?
Either: https://online.vitalsource.com or https://evantage.gilmoreglobal.com

### Isn't this an AWS course? Don't AWS have very close ties to a company that's very good at eBook distribution? Why aren't these books in Kindle format?
Good question. The answer, I'm told, is something to do with tax. Different US states have different tax regimens, so they'd have to charge different amounts in different states. Or something. Gilmore is a Canadian-based and well-respected eBook delivery platform in the academic space.

## 2 - Lab environment

### What is this qwikLABS thing?
Essentially, it's a pool of AWS accounts that belong to QA, which we can enable you to have access to for a specific period of time. It is owned and run by a third party, completely separate from ourselves and AWS.

It means that you don't need to have your own AWS account in order to complete the labs in our courses.

Some labs need to have some AWS resources "ready made", so the environment allows your trainer to create those resources in advance.

The environment also allows us to restrict exactly what you can do in the AWS account, limiting it to what you need to be able to do to complete the lab.

Finally, the environment also tidies up after itself, removing all of the resources that were created during the lab either when the lab times out or when you click on the "End Lab" button. [**Note** strictly speaking, there are some side effects of some labs that cannot be removed as there are no API calls that enable this, but it does its best. It is also not an instantaneous process - accounts get queued up for "cleaning" once the lab ends.]

### What's the url again?
It's *very* easy to mis-spell (or forget how to mis-spell) the URL: https://qa.qwiklab.com.

### Can I have extended access to the labs?
Sadly no. QA is charged per delegate per day for qwikLABS access, so that cost is factored into the price for your course.

### How can I experiment with AWS then?
You have several options:
1. You can sign up for an AWS account and get on what they call the "Free Tier", which is basically just enough freebies to run a very small website for a year. It also possible to monitor your free tier usage: http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html. Please note that not all services are included in the free tier.
2. Sign up for an account at https://run.qwiklab.com. This is a separate qwiklab environment with a separate user database. You can purchase tokens which enable you to complete labs, many of which are similar to the labs you will have completed during your course. Tokens can get expensive though - one token costs one dollar and some labs cost as much as 15 tokens - so in many cases experimenting on your own account will probably work out cheaper. run.qwiklab.com also has an "all you can eat" option, the Advantage Subscription, which provides unlimited access to all labs for 55 dollars a month.
3. Many organisations have some kind of lab environment account(s). You may be able to gain access to such an account.

**NOTE** you cannot set spending limits in AWS, only notifications for estimated monthly charges, so when experimenting, remember the mantra: **D**eploy, **U**se, **D**estroy.
