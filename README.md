# GitHub Desktop that doesn't have auto-update enabled

It's simple. The official build doesn't respect the word no.

# An open letter to GitHub Desktop team

I've always wanted to create an issue within the Github repo with these contents, but decided against it. So, I just decided to create a fork with the fixes I wanted and leave the letter here anyway:

First of all,
I understand GitHub Desktop is an open source product. So, thank you to all the maintainers for all your efforts so far for your thankless hours poured into the project, I truly appreciate and respect that.

But having said that, GitHub Desktop also falls under the umbrella of Microsoft (GitHub Inc.) and is a far cry from a regular open source product. In fact, we can even consider it an integral part of GitHub Inc.'s offering: 

https://desktop.github.com/download/

You will see in the bottom: "© 2025 GitHub, Inc. All rights reserved." - Implying that this is directly Intellectual Property owned in part or whole by GitHub Inc.

I establish this first hand so that the standard defence lines "Oh, we're just an independent open source project" do not apply. This tool can be even considered as Microsoft's competitive advantage in this space.

This is where in the problem lies. GitHub Desktop does not respect the user's requests when they decline any sort of update install or helper tool installation. It consistently harasses the user. Why is this considered harassment? Because, it does not respect the word "No". Consistently. Every day, this pattern is part of a standard ritual - harass the user till they accept "Yes". Every single day. The user needs to decline 5 times or even more in a given day and even then, there is no guarantee of the popup harassment leaving the user alone. 

Sometimes, I could be in the middle of some critical work and the popup could interrupt me at the worst possible moment (eg. doing CNC work). You don't even need to be using the software, just leaving it open in the background leads to the harassment. I've lost so much stock wood because GitHub interrupted me in the middle of critical CNC work.

Here is documented video evidence of this behaviour:

https://github.com/user-attachments/assets/a440316a-56a6-46e8-bf39-519d668b353b

The team is aware of this issue since 2023 and outright refuses to do anything about this, because, they somehow believe it is ok to harass their users. And they are very explicit about it too:

_"Disabling auto-update is not in our roadmap, sorry 😕 But updating it after closing the app should be pretty quick,"_

Source: https://github.com/desktop/desktop/issues/16227#issuecomment-1449596959

And this isn't the first instance of this issue, there's a few more:

https://github.com/desktop/desktop/issues/3410

GitHub is a monopoly in this space. This just comes off as abusing your monopoly privileges. Guess this behaviour is common across all of Microsoft's product line (Windows, VS Code, GitHub Desktop, etc.) but that doesn't make it ok.

You don't have to disable auto update - that's completely fine, this is your product, after all. But, the least you can do is have the basic human decency to respect when a user says "No". No means no. No doesn't mean "I will eventually say yes" after constant harassment.
No doesn't mean "But updating it after closing the app should be pretty quick".

We are all software engineers who use GitHub everyday and hopefully we can have our "No" respected within a tool that is centric to our lives.

Thank you.



# [GitHub Desktop](https://desktop.github.com)

[GitHub Desktop](https://desktop.github.com/) is an open-source [Electron](https://www.electronjs.org/)-based
GitHub app. It is written in [TypeScript](https://www.typescriptlang.org) and
uses [React](https://reactjs.org/).

<picture>
  <source
    srcset="https://user-images.githubusercontent.com/634063/202742848-63fa1488-6254-49b5-af7c-96a6b50ea8af.png"
    media="(prefers-color-scheme: dark)"
  />
  <img
    width="1072"
    src="https://user-images.githubusercontent.com/634063/202742985-bb3b3b94-8aca-404a-8d8a-fd6a6f030672.png"
    alt="A screenshot of the GitHub Desktop application showing changes being viewed and committed with two attributed co-authors"
  />
</picture>

## Where can I get it?

Download the official installer for your operating system:

 - [macOS](https://central.github.com/deployments/desktop/desktop/latest/darwin)
 - [macOS (Apple silicon)](https://central.github.com/deployments/desktop/desktop/latest/darwin-arm64)
 - [Windows](https://central.github.com/deployments/desktop/desktop/latest/win32)
 - [Windows machine-wide install](https://central.github.com/deployments/desktop/desktop/latest/win32?format=msi)

Linux is not officially supported; however, you can find installers created for Linux from a fork of GitHub Desktop in the [Community Releases](https://github.com/desktop/desktop#community-releases) section.

### Beta Channel

Want to test out new features and get fixes before everyone else? Install the
beta channel to get access to early builds of Desktop:

 - [macOS](https://central.github.com/deployments/desktop/desktop/latest/darwin?env=beta)
 - [macOS (Apple silicon)](https://central.github.com/deployments/desktop/desktop/latest/darwin-arm64?env=beta)
 - [Windows](https://central.github.com/deployments/desktop/desktop/latest/win32?env=beta)
 - [Windows (ARM64)](https://central.github.com/deployments/desktop/desktop/latest/win32-arm64?env=beta)

The release notes for the latest beta versions are available [here](https://desktop.github.com/release-notes/?env=beta).

### Past Releases
You can find past releases at https://desktop.githubusercontent.com. After installation of a past version, the auto update functionality will attempt to download the latest version. 

### Community Releases

There are several community-supported package managers that can be used to
install GitHub Desktop:
 - Windows users can install using [winget](https://docs.microsoft.com/en-us/windows/package-manager/winget/) `c:\> winget install github-desktop` or [Chocolatey](https://chocolatey.org/) `c:\> choco install github-desktop`
 - macOS users can install using [Homebrew](https://brew.sh/) package manager:
      `$ brew install --cask github`

Installers for various Linux distributions can be found on the
[`shiftkey/desktop`](https://github.com/shiftkey/desktop) fork.

## Is GitHub Desktop right for me? What are the primary areas of focus?

[This document](https://github.com/desktop/desktop/blob/development/docs/process/what-is-desktop.md) describes the focus of GitHub Desktop and who the product is most useful for.

## I have a problem with GitHub Desktop

Note: The [GitHub Desktop Code of Conduct](https://github.com/desktop/desktop/blob/development/CODE_OF_CONDUCT.md) applies in all interactions relating to the GitHub Desktop project.

First, please search the [open issues](https://github.com/desktop/desktop/issues?q=is%3Aopen)
and [closed issues](https://github.com/desktop/desktop/issues?q=is%3Aclosed)
to see if your issue hasn't already been reported (it may also be fixed).

There is also a list of [known issues](https://github.com/desktop/desktop/blob/development/docs/known-issues.md)
that are being tracked against Desktop, and some of these issues have workarounds.

If you can't find an issue that matches what you're seeing, open a [new issue](https://github.com/desktop/desktop/issues/new/choose),
choose the right template and provide us with enough information to investigate
further.

## The issue I reported isn't fixed yet. What can I do?

If nobody has responded to your issue in a few days, you're welcome to respond to it with a friendly ping in the issue. Please do not respond more than a second time if nobody has responded. The GitHub Desktop maintainers are constrained in time and resources, and diagnosing individual configurations can be difficult and time consuming. While we'll try to at least get you pointed in the right direction, we can't guarantee we'll be able to dig too deeply into any one person's issue.

## How can I contribute to GitHub Desktop?

The [CONTRIBUTING.md](./.github/CONTRIBUTING.md) document will help you get setup and
familiar with the source. The [documentation](docs/) folder also contains more
resources relevant to the project.

If you're looking for something to work on, check out the [help wanted](https://github.com/desktop/desktop/issues?q=is%3Aissue+is%3Aopen+label%3A%22help%20wanted%22) label.

## Building Desktop

To setup your development environment for building Desktop, check out: [`setup.md`](./docs/contributing/setup.md).

## More Resources

See [desktop.github.com](https://desktop.github.com) for more product-oriented
information about GitHub Desktop.

See our [getting started documentation](https://docs.github.com/en/desktop/overview/getting-started-with-github-desktop) for more information on how to set up, authenticate, and configure GitHub Desktop.

## License

**[MIT](LICENSE)**

The MIT license grant is not for GitHub's trademarks, which include the logo
designs. GitHub reserves all trademark and copyright rights in and to all
GitHub trademarks. GitHub's logos include, for instance, the stylized
Invertocat designs that include "logo" in the file title in the following
folder: [logos](app/static/logos).

GitHub® and its stylized versions and the Invertocat mark are GitHub's
Trademarks or registered Trademarks. When using GitHub's logos, be sure to
follow the GitHub [logo guidelines](https://github.com/logos).
