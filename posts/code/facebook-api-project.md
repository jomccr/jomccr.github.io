Wed, Oct 28, 2020 10:13:36 AM

----

## Facebook Graph API 

At work, we're having to use the facebook graph api to upload custom advertising audiences into their ads manager platform. We're doing this for a client that has a list of email addresses with accounts on their platform and they're running facebook ads/promotions targeting these users. We refresh these audiences every month. 

When I got here, the process was nearly 100% manual. The actual audiences had been created in the platform, and our monthly processing spit out two files for each audience - adds and deletes. We hash the emails (sha256) and facebook looks for matches in their system. 

See the [API reference](https://developers.facebook.com/docs/marketing-api/reference/custom-audience) for more general info about facebook custom audiences.

## Automating 

I work on a team of people who are most familiar with bash, SQL, and perl. If I was doing this on my own I would have used Python, but I don't want to be the only one who can hack on the code. :) 

The process for updating a custom audiencd (with adds or deletes) comes down to a HTTP POST or DELETE request, respectively. We are still in development at the moment, so this is more of a journal of my progress. I believe there is no change to the underlying data/payload sent to facebook, we just have to alternate between POST/DELETE accordingly. The "secret sauce" as my coworker put it, is perl's [LWP::UserAgent](https://metacpan.org/pod/LWP::UserAgent) module. It makes pragmattically generating HTTP requests very simple. 

```perl
if ($method == 'POST') {
    $res = $ua->post("https://graph.facebook.com/v8.0/$audience_id/users",
                [
                    access_token => $token,
                    data => $req_json
                ]
            );
}
```

## Tracking Progress

One thing that is bothering me, is designing a way to track the uploads as they happen. 

