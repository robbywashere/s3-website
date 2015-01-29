## s3-website

Easily publish static websites to Amazon S3. TLS encryption can be enabled via Cloudfront.

Creates a bucket with the specified name and enables static website hosting on it. Also, sets up a public-read bucket policy.

Your AWS credentials should either be in `~/.aws/credentials` or in the environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

Your website policy and configuration will only be sent to S3 when it differs
from the existing.

### Note!

Because of limitations of the S3 API, any changes made to the website policy or
configuration in the S3 web interface, or elsewhere, will be overwritten by the
settings provided to `s3-website`.

### Installation

`s3-website` is a [node.js](http://nodejs.org) program/module.

```
npm install -g s3-website
```

### Usage (CLI)

```
$ s3-website -h

  Usage: s3-website [options] domain

  Options:

    -h, --help                           output usage information
    -V, --version                        output the version number
    -r, --region <region>                Region [us-east-1]
    -i, --index <index>                  Index Document [index.html]
    -e, --error <error>                  Error Document
    -t, --routes <routes>                Path to a require'able RoutingRules file
    --json                               Output JSON
    --cert-id <IAM ServerCertId>         The ID of your cert in IAM.
    -c, --cert <cert>                    Path to the public key certificate.
    -k, --key <key>                      Path to the private key.
    -n, --cert-name <certificate name>   A unique name for the server certificate.
    --intermediate <intermediate certs>  Path to the concatenated intermediate certificates.

```
All the options are optional ;-).

`s3-website -r eu-central-1 cool.website.me` creates a website bucket with the following URL `http://cool.website.me.s3-website.eu-central-1.amazonaws.com`.
You can then set up a CNAME record for `cool.website.me` pointing to that hostname.

For the TLS related options take a look at the [cloudfront-tls](https://github.com/klaemo/cloudfront-tls) readme.

### Usage (API)

```javascript
var s3site = require('s3-website')

s3site({
  domain: 'test.site.me', // required, will be the bucket name
  region: 'eu-central-1', // optional, default: us-east-1
  index: 'index.html', // optional index document, default: index.html
  error: '404.html' // optional error document, default: none
  routes: [{
    Condition: {
        KeyPrefixEquals: 'foo/'
    },
    Redirect: {
        HostName: 'foo.com'
    }
  }]
}, function(err, website) {
  if (err) throw err
  console.log(website)
})
```

You can also pass in the same the TLS related options as in [cloudfront-tls](https://github.com/klaemo/cloudfront-tls). So you might want to take a look at its readme if you want to use your own certificates.

### Routing Rules

`RoutingRules` can be provided via cli and API. From the cli you will need to provide the path to
a file that can be loaded via `require`, that is to say, a `.js` or `.json` file. This file
should export an array of rules that conform to the [S3 Routing Rules syntax](http://docs.aws.amazon.com/AmazonS3/latest/dev/HowDoIWebsiteConfiguration.html#configure-bucket-as-website-routing-rule-syntax). Likewise, you can provide an
array of rules to the API with the `routes` option.

### TODO

- www -> non-www redirect (via Route 53 hack)

### Contributors

- [mshick](https://github.com/mshick)

### License
ISC
