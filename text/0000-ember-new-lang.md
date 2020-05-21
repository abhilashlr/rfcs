- Start Date: (fill me in with today's date, YYYY-MM-DD)
- Relevant Team(s): (fill this in with the [team(s)](README.md#relevant-teams) to which this RFC applies)
- RFC PR: (after opening the RFC PR, update this with a link to it and update the file name)
- Tracking: (leave this empty)

# Ember New Lang

## Summary

This RFC introduces the `--lang` flag as an option for `ember new` and `ember init` within Ember-CLI. If used with a valid language code, the flag will set the `lang` attribute on the `<html>` element at the new application's `app/index.html` entry point. Assigned correctly, `lang` attribute defines the base human language of an element or a document in a way that can be programmatically understood by assistive technology.

## Motivation

The overall motivation for this RFC is the belief that new ember apps should not immediately fail legal conformance requirements as they pertain to digital accessibility.

The solution presented in this RFC offers the first stage of a resolution to one of the issues documented in the framework's [long-standing list of Technical Accessibility Issues for New Ember Apps](https://github.com/emberjs/rfcs/issues/595) -- specifically, *“Missing default language declaration”* (Section 4). 

The  has been developed within the Ember.js Accessibility Strike Team with the objective of helping to ensure that Ember applications achieve [WCAG Success Criterion 3.1.1: Language of Page](https://www.w3.org/WAI/WCAG21/Understanding/language-of-page.html) from the moment they are created. For users of screen-reading assistive technology, the impact of having a properly assigned `lang` attribute is important:

> Both assistive technologies and conventional user agents can render text more accurately when the language of the Web page is identified. Screen readers can load the correct pronunciation rules. Visual browsers can display characters and scripts correctly. Media players can show captions correctly. **As a result, users with disabilities will be better able to understand the content.**
> 
> **-- <cite>[WCAG Success Criterion 3.1.1: Intent](https://www.w3.org/WAI/WCAG21/Understanding/language-of-page.html#intent)</cite>**

Furthermore, having a page language specified should improve the user experience and technical improvements around these areas:

- Screen readers, braille translation software and similar technologies
- Captions with synchronized media (such as video subtitles)
- Correct dictionary lookups for translations
- Assisting search engines
- Improving typography in certain situations

## Detailed design

Link to [candidate implementation](https://github.com/josephdsumner/ember-cli/compare/master...josephdsumner:ember-new-lang-util).

```bash
ember new my-app --lang en-US
# -l are is a valid alias as well
```

The above ember-cli command will result in the following `index.html` header change.

```html
<html lang="en-US">
```

The flag is added to relevant ember-cli help commands, such as the following:

```bash
ember help new
ember new <app-name> <options...>
  Creates a new directory and runs ember init in it.
  --dry-run (Boolean) (Default: false)
    aliases: -d
  --verbose (Boolean) (Default: false)
    aliases: -v
  --blueprint (String) (Default: app)
    aliases: -b <value>
  --skip-npm (Boolean) (Default: false)
    aliases: -sn
  --skip-bower (Boolean) (Default: false)
    aliases: -sb
  --skip-git (Boolean) (Default: false)
    aliases: -sg
  --welcome (Boolean) (Default: true) Installs and uses {{ember-welcome-page}}. Use --no-welcome to skip it.
  --yarn (Boolean)
  --directory (String)
    aliases: -dir <value>
  --lang (String) (Default: "") Sets the base human language of the application via index.html
    aliases: -l <value>
```

### Invalid Language Codes

Language codes are verified against [is-language-code](https://www.npmjs.com/package/is-language-code). (see [examples of valid ISO country codes](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes))

If an invalid language code is given such as `--lang en-UK` the intended output should be a shell error that will halt the build.

```bash
ember new my-app --lang en-UK
Unrecognised language subtag, "uk".
```

#### Common Misunderstandings

A developer may encounter the flag and make incorrect assumptions about what it can mean. Such as, `-l typescript` or `-l glimmer`. Such incorrect assumptions will be manually caught by the implementation and the developer will be shown a friendly error message such as the following:

```bash
ember new my-app --lang typescript
Trying to set the app programming language to typescript? The `--lang` flag sets the base human language of the app in index.html
```

## How we teach this

1. Update the [Ember CLI API documentation](https://ember-cli.com/api/) to reflect the new flag.
2. Update the [Ember.js CLI Guides](https://cli.emberjs.com/release/basic-use/cli-commands/) to reflect the new flag much like we demonstrate `--yarn` usage.
3. Update the Ember CLI `--help` command so it explains what kind of value is expected to be passed to the `--lang` flag.

## Drawbacks

* More flags means more combinations of ways to run `ember new` which can be hard to test for and is potentially unsustainable.
* Users may be confused about whether they’re supposed to specify a human language or a programming language (i.e. `--lang typescript`).

## Alternatives

Do nothing.

## Unresolved questions

* Can the default lang value simply be English? ([Valuable discussion points in this issue](https://cli.emberjs.com/release/basic-use/cli-commands/))

## References

* [Understanding Success Criterion 3.1.1: Language of Page](https://www.w3.org/WAI/WCAG21/Understanding/language-of-page.html)
* [Technique H57: Using the language attribute on the HTML element](https://www.w3.org/WAI/WCAG21/Techniques/html/H57)
* [HTML5 Specification: `lang` attribute](https://html.spec.whatwg.org/#the-lang-and-xml:lang-attributes)