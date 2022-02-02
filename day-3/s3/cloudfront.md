# CloudFront

## Content Distribution Network (CDN)

So we learned that we can replicate the contents of an S3 bucket to a different region, for example to reduce latency. What if we want to serve content globally to people all around the world? Do we set up buckets in every region and replicate everywhere just in case? Nah. We use CloudFront, AWS's CDN solution.&#x20;

Amazon CloudFront speeds up distribution of your static and dynamic web content, such as .html, .css, .js, and image files, to your users. CloudFront delivers your content through a worldwide network of data centers called edge locations. When a user requests content that you're serving with CloudFront, the request is routed to the edge location that provides the lowest latency (time delay), so that content is delivered with the best possible performance.

* If the content is already in the edge location with the lowest latency, CloudFront delivers it immediately.
* If the content is not in that edge location, CloudFront retrieves it from an origin that you've defined—such as an Amazon S3 bucket, a MediaPackage channel, or an HTTP server (for example, a web server) that you have identified as the source for the definitive version of your content.

## Concepts

### Distribution&#x20;

When you want to use CloudFront to distribute your content, you create a distribution and choose the configuration settings you want. These settings include the origin (the content you want to distribute), possible security settings, geo-location restrictions and so on.&#x20;

### Edge locations and regional caches

Regional edge caches are CloudFront locations that are deployed globally, close to your viewers. They're located between your origin server and the POPs—global edge locations that serve content directly to viewers. As objects become less popular, individual POPs might remove those objects to make room for more popular content. Regional edge caches have a larger cache than an individual POP, so objects remain in the cache longer at the nearest regional edge cache location.

### Signed URL's and signed cookies

If you want to serve some content only to certain users, say paid users, then you can restrict access to private content with signed URL's or cookies.&#x20;

Use signed URLs in the following cases:

* You want to restrict access to individual files, for example, an installation download for your application.
* Your users are using a client (for example, a custom HTTP client) that doesn't support cookies.

Use signed cookies in the following cases:

* You want to provide access to multiple restricted files, for example, all of the files for a video in HLS format or all of the files in the subscribers' area of website.
* You don't want to change your current URLs.

### Origin Access Identity (OAI)

Suppose you have an S3 bucket that you want to use as the origin of a CloudFront distribution. You want all the users to access the content through CloudFront, not the S3 bucket directly. Then you can create an origin access identity (OAI), which is a special CloudFront user, and associate the origin access identity with your distribution.&#x20;
