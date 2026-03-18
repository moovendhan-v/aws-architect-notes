# Getting Started with AWS


## AWS Cloud History

2002: 2004: 2007:
Internally Launched publicly Launched in
launched with SQS Europe
2003: 2006:
Amazon infrastructure is Re-launched
one of their core strength. publicly with
Idea to market SQS, S3 & EC2


## AWS Cloud Number Facts

- In 2023, AWS had $90 billion
    - in annual revenue
- AWS accounts for 31% of the
    - market in Q1 2024 (Microsoft
is 2nd with 25%)
- Pioneer and Leader of the
    - AWS Cloud Market for the
13th consecutive year
- Over 1,000,000 active users
    - Gartner Magic Quadrant


## AWS Cloud Use Cases

- AWS enables you to build sophisticated, scalable applications
- Applicable to a diverse set of industries
- Use cases include
- Enterprise IT, Backup & Storage, Big Data analytics
- Website hosting, Mobile & Social Apps
- Gaming


## AWS Global Infrastructure

- AWS Regions
- AWS Availability Zones
- AWS Data Centers
- AWS Edge Locations /
    - Points of Presence
- https://infrastructure.aws/


## AWS Regions

- AWS has Regions all around the world
- Names can be us-east-1, eu-west-3…
- A region is a cluster of data centers
- Most AWS services are region-scoped
    - https://aws.amazon.com/about-aws/global-infrastructure/


## How to choose an AWS Region?

If you need to launch a new application,
- Compliance with data governance and legal
    - where should you do it?
requirements: data never leaves a region without
your explicit permission
?
?
- Proximity to customers: reduced latency
- Available services within a Region: new services
    - ? ?
and new features aren’t available in every Region
- Pricing: pricing varies region to region and is
    - transparent in the service pricing page


## AWS Availability Zones

- Each region has many availability zones
    - AWS Region
(usually 3, min is 3, max is 6). Example:
Sydney: ap-southeast-2
- ap-southeast-2a
- ap-southeast-2b
    - ap-southeast-2a
- ap-southeast-2c
- Each availability zone (AZ) is one or more
    - discrete data centers with redundant power,
networking, and connectivity
- They’re separate from each other, so that
    - ap-southeast-2b ap-southeast-2c
they’re isolated from disasters
- They’re connected with high bandwidth,
    - ultra-low latency networking


## AWS Points of Presence (Edge Locations)

- Amazon has 400+ Points of Presence (400+ Edge Locations & 10+
    - Regional Caches) in 90+ cities across 40+ countries
- Content is delivered to end users with lower latency
    - https://aws.amazon.com/cloudfront/features/


## Tour of the AWS Console

- AWS has Global Services:
- Identity and Access Management (IAM)
- Route 53 (DNS service)
- CloudFront (Content Delivery Network)
- WAF (Web Application Firewall)
- Most AWS services are Region-scoped:
- Amazon EC2 (Infrastructure as a Service)
- Elastic Beanstalk (Platform as a Service)
- Lambda (Function as a Service)
- Rekognition (Software as a Service)
- Region Table:

https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services
