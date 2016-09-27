## Introduction

Amnesiac Aardvark is a Pale Moon add-on that blocks spying ads and invisible trackers as you browse. [More info here.](https://www.eff.org/privacybadger)

Amnesiac Aardvark was forked from the EFF's Privacy Badger extension. It is not the goal of the developer to create a separate and distinct product, but rather to simply offer Privacy Badger's code and features to Pale Moon users. Therefore, we will be integrating upstream changes as they become available.

## Developers' guide

### Building
Compress the **contents** of src/ into a zip file, and rename the extension of the file from .zip to .xpi.
Note: if you zip src itself the extension will not work!

Then drag the .xpi file into a Pale Moon window and follow the prompts to install.


### Important directories and files

    package.json              |
    data/                     |
    lib/                      | Most of the code that runs in the add-on. See SDK documentation for more info on the directory structure.
    locale/                   |
    defaults/                 |

    doc/                      Changelog, style guide, how to make a signed release, other documentation TBD.

### Contributing

Before you submit a pull request please consult the [CONTRIBUTING.md](./CONTRIBUTING.md) file.

## How heuristic blocking works

This is a rough summary of Amnesiac Aardvark's internal logic for blocking trackers. At the moment, "tracker" == "third-party cookie from a site that tracks you on multiple first-party origins." The Privacy Badger developers are in the process of adding support for other non-cookie tracker types (local storage, etags, cache hits, etc.), which will be forked to Amnesiac Aardvark upon completion.

Amnesiac Aardvark uses a (relatively-simple) heuristic algorithm for deciding whether a third-party is tracking you. When Amnesiac Aardvark sees a third-party request on a website, it checks:

1. Does the third-party read a cookie? If not, don't count it in the blocking heuristic. Otherwise:
2. Is the cookie sufficiently high-entropy? If not, don't count it. (Currently the entropy calculation is *very* crude! See lib/heuristicBlocker.js.) Otherwise:
3. Increment the heuristic blocker counter by +1 for that domain. Has the base domain (eTLD+1) of the third-party read cookies on at least 3 first-party base domains? If not, don't block it (for now). Otherwise:
4. Has the third party posted an acceptable DNT policy? (We check this using an XML HTTP Request to a well-known path where we are asking sites to post statements of [compliance with DNT](https://www.eff.org/dnt-policy).) If so, don't block it. Otherwise:
5. Is the third party or any of its parent domains on a preloaded whitelist of sites to not block because it would probably cause the first-party site to break? If so, block it from reading cookies in a third-party context. Otherwise:
6. Block third-party requests from the third-party entirely.

In addition, Amnesiac Aardvark will block third-party cookies from a domain if any of its parent domains have been blocked or cookie-blocked.

Note that users can manually set domains to be unblocked (green), cookie-blocked (yellow), or red (blocked). These choices *always* override the heuristic blocker.

By default, Amnesiac Aardvark sends the Do Not Track header on all requests. It also clears the referer for all requests that are cookie-blocked.

## Contact
The maintainer of Amnesiac Aardvark is Fred-Barclay (Bugs Ate Fred at gmail dot com).
If possible, please open an Issue to contact me. If you would prefer not, then feel free to email.


The current maintainers of Privacy Badger are  Cooper Quintin (cjq at eff dot org) and Noah Swartz (noah at eff dot org). There is also a [mailing list](https://lists.eff.org/mailman/listinfo/privacybadger) to discuss Privacy Badger development for both Firefox and Chrome.  

Please note that though we applaud the Privacy Badger developers and would not exist without them, Amnesiac Aardvark is not officially affiliated with Privacy Badger. Please open issues here, as the Privacy Badger developers will not be able to offer assistance.
