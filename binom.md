# Integration with Binom

Binom tracker versions 1.16&ndash;1.17.122 come with native Adspect integration (coming soon in versions 2.x.)
Tracker distributions include Adspect Sieve™ traffic filtering software that is locally installed
on the same server as the tracker itself.  This software is different from the cloud-based Adspect
service in several major ways:

1.  Traffic filtering takes place locally on your server, i.e. the software does not make requests
    to the Adspect cloud in real time, resulting in dramatic increase in click processing speed.
    Adspect Sieve is written in C++ and sports tremendous processing power limited only by your
    server hardware and network bandwidth.

2.  No click logs or other data is sent to Adspect.  Your statistics are kept private on your server.
    The software only performs background synchronization of filters and subscription status every
    once in a while.  Even if it cannot contact Adspect servers or if your subscription expires it
    will keep working in fully autonomous mode for 3 days, giving you a grace period.

3.  Adspect streams are not used; Binom campaigns replace them.  You do not need to do anything
    in the Adspect cloud service beyond purchasing a subscription and obtaining credentials.

Adspect Sieve is essentially a standalone filtering engine that implements [the core of Adspect
know-how technology](how-it-works.md): blocklisting, JavaScript fingerprinting, and [VLA™ machine
learning](how-it-works.md#machine-learning).  The rest of the filters available in the cloud service (such as targeting,
custom IP address blocklists, click limits, etc.) are covered by the functionality of Binom tracker.

## Installation

Adspect Sieve distribution is bundled with Binom.  Installation takes three simple steps:

1.  [Connect to your server via SSH](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-to-connect-to-a-remote-server);

2.  Execute the following command with superuser privileges (log in as root or use `sudo`):
    ```sh
    bash /root/binom_install.sh adspect
    ```

3.  At the prompt (see screenshot below) type `1` for "Yes" and press Enter.
    ```{image} _static/binom/installation.png
    :alt: Successful installation will print the "Done!" message.
    ```

## Configuration

In order to use Adspect Sieve you will need to pair it with an Adspect account using its ID and API key.
You will find both in [your profile](https://clients.adspect.ai/profile) in Adspect clients area:

```{image} _static/binom/credentials1.png
:alt: Adspect Sieve credentials in profile
```

In Binom, go to campaign settings, open the **Protection** section, tick the **Adspect** checkbox there,
then click **Add account**.  A dialog window will appear; enter account ID and API key in there and
press the **Save** button:

```{image} _static/binom/credentials2.png
:alt: Adspect Sieve credentials in Binom
```

If you need to change these credentials later, then you may do so in **Settings > API**.

## Enabling Filtering

Adspect Sieve filtering is enabled per-campaign with a [campaign rule](https://docs.binom.org/campaign-rules.php).
You may use either **Bot** or **NOT Bot** rules to route traffic, depending on the logic you need:

```{image} _static/binom/bot-rule.png
:alt: Binom Bot rule example
```

Just like the cloud version of Adspect platform, Adspect Sieve supports JavaScript fingerprinting
technology to perform advanced, in-depth scanning of each visitor.  It may be enabled in campaign
settings under the **Protection** menu--tick the **Use fingerprint** checkbox there.

Binom [Click API](https://docs.binom.org/click-api.php) fully supports Adspect Sieve integration,
including JavaScript fingerprinting.

### JS Protection

Turning on Adspect Sieve filtering will change [JS Protection](https://docs.binom.org/js-protection.php)
script code, so if you use it, then make sure to replace your existing scripts with new ones.
