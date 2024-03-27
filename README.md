# Mutable Web 4 B.O.S - Releases 1 & 2 (initial BOS MVP and web2 support)  

## Purpose of our work

We have implemented Mutable Web on top of the current B.O.S architecture and released it as a Pull Request. It is our contribution to the B.O.S core development. We hope Mutable Web will become the standard functionality of B.O.S in the future. Release 2 introduced the Mutable Web support for web2 websites.

## TLDR; Demos and videos 
   * **Release 1:** Near Social running on our Mutable Web Gateway.  
     Watch the demo video and try the [Demo](https://social.dapplets.org).
   * **Release 2:** Twitter on Mutable Web.  
     Watch the demo video and try the [Mutable Twitter Demo](https://augm.link/mutate?t=https://twitter.com/MrConCreator&m=bos.dapplets.near/mutation/Sandbox).  
    (*You will be asked to install the Mutable Web extention*).


for the Long Read continue reading...

## Introduction

1. The usual web2 paradigm assumes the website Owner is the only one in control of the website and, thus, the functionality delivered to the public. The legacy web is the Owner-controlled one.

2. The B.O.S approach improves it and allows any Developer to create a fork of the original website because the well-built B.O.S website is composed of reusable components and publicly accessible data. Therefore, the B.O.S web is a Developer-controlled one. It is a vast improvement, but it still has flaws: it doesn’t work on existing web2 websites, forked B.O.S websites inherit no usage of the original one, and the Developer role is still quite a high entry barrier for ordinary Users, who are still only able to choose which website to use but have no power to take over the control over the functionality used.

3. Mutable Web improves the B.O.S approach and allows sub-communities and single users to create and use customized versions of someone’s website on the fly without permission while remaining part of the whole original Community. Moreover, this works both on B.O.S-based websites and in legacy web2. Therefore, the Mutable Web is genuinely Community-controlled because it is the Community that decides which functionality is being used - the website owner has no power anymore to enforce his rules in the long term.

4. The social and business implications of the Mutable Web paradigm are massive. Mutable Web allows Communities to create customized versions of the original websites that best serve their needs. On the other hand, the Owner no longer needs to create one website for anyone to meet the controversial expectations of different user groups - any sub-community can create customized versions of the original website to serve its needs. Communities can resist unwanted website changes and forced actions. Mutable Web creates a new economy model where communities become resilient and self-sovereign in economy and governance even if they do not own their websites. Mutable Web also incentivizes Owners to tokenize their business and join the Community instead of opposing it. The Mutable Web paradigm also improves the development process by outsourcing it entirely to the Community – the Owner only needs to cherry-pick ready and battle-tested features.

5. You can read more about the Mutable Web in its [Manifesto](./mwm.md).

## How Mutable Web is organized

1. In Mutable Web, every Community can run DAOs to create and manage customized versions of the original website on which it lives. This customized website version is called Mutation. It consists of one or many reusable Applications implementing the necessary functionality. An actor (usually a DAO) managing the Mutation is called a Mutator.

2. Mutators know both the Community's needs and the Applications available in the Mutable Web ecosystem to meet them. Many Mutations can compete in the Community; the best one will attract the most users. Mutators are not intended to develop Applications. Their job is to pick the best apps and package them into Mutation. Applications are being developed by independent Developers.

3. Regular Users don't need to bother with technical details. As a Community member, they can follow the most popular Mutation of their Community and become the desired functionality out of the box. The relationship between Users, Mutators, and application Developers in the Mutable Web is very similar to those of Users, Distributors, and Developers in the Linux community.

## Functionality of Mutations

1.  _Widget Injection_ :

    a. Every Mutation consists of Applications implementing particular business cases.

    ```javascript
    Provider.createMutation({
      "id": "bos.dapplets.near/mutation/Sandbox",
      "metadata": {
        "name": "Sandbox",
        "description": "Playground where anyone can inject BOS components",
        "image": {
          "ipfs_cid": "bafkreihxajq5ujkgp4t7uadmymet4s62z2rnqb22okpw4vb7atichoq7oy"
        }
      },
      "apps": ["bos.dapplets.near/app/Paywall","bos.dapplets.near/app/Tipping"]
    })
    ```

    b. Applications consist of a Controller and a bunch of Widgets implemented as B.O.S components, which are injected into the target website.

    ```javascript
    Provider.createApplication({
      "id": "bos.dapplets.near/app/Paywall",
        "metadata": {
        "name": "Paywall",
        "description": "The Paywall Dapplet seamlessly integrates with Twitter, utilizing the NEAR Protocol and NEAR BOS to display paid content, a solution developed during the Web3 Hackfest 2023 hackathon.",
        "image": {
          "ipfs_cid": "bafkreihxajq5ujkgp4t7uadmymet4s62z2rnqb22okpw4vb7atichoq7oy"
        }
      },
      "targets": [{
        "namespace": "https://dapplets.org/ns/json/bos.dapplets.near/parser/twitter",
        "contextType": "post",
        "if": { "id": { "not": null, "index": true } }, // <--- condition
        "injectTo": "afterText",                        // <--- injection point
        "componentId": "bos.dapplets.near/widget/Paywall.Main"
      },{
        "namespace": "https://dapplets.org/ns/bos/bos.dapplets.near/parser/near-social",
        "contextType": "post",
        "if": { "id": { "not": null, "index": true } },
        "injectTo": "afterText",
        "componentId": "bos.dapplets.near/widget/Paywall.Main"
      }]
    })
    ```

    c. The injection takes place in the User's browser. No server is getting hacked, and no further access needs to be granted. If you are familiar with aspect-oriented programming (AOP), you can think about Mutable Web as the web of aspects created by the Community. These web aspects can be applied to the websites no matter who the Owner is.

    d. To implement the AOP paradigm, Mutable Web introduces InjectionLink. It is a simple data structure instructing the Core to insert a specific B.O.S-Component under specific conditions at some point of a particular Website. These injection links are stored alongside the existing B.O.S components in the component storage.

    e. For every Widget aimed to be injected, the Application Developer defines at least one injection link to specify the injection point and the injection condition.

    f. A Mutator can adjust (overload) injection links of the Applications in the Mutation. By doing that, he can redefine the visibility of the Application or attach it to new types of content.

    g. A User can also create an injection link. Example: User places a sticker on a tweet of his choice.

    ```javascript
    Provider.createLink({
      appId: "bos.dapplets.near/app/Paywall",
      mutationId: "bos.dapplets.near/mutation/Sandbox",
      namespace: "https://dapplets.org/ns/json/bos.dapplets.near/parser/twitter",
      contextType: "post",
      if: {
        id: "123123213123"
      }
    })

    ```

2.  _Context_:

    a. Every Widget is aware of the Context where it got injected and operates. Context is any meaningful semantical data extracted from the website and cleaned from the markup. For example, if the Tipping Widget sends a tip to the post's author, the authorId comes from the Context object.

    b. A ParserConfig is a separate entity created by an independent Developer containing the knowledge about the semantical structure of the raw source, like a website. It defines parsing rules, Context types, injection points, and related Layout Managers. The Application Developer is responsible for choosing a suitable ParserConfig for his Application working in a specific Context.

    c. LayoutManager is a B.O.S component that renders other injected components.

    d. A Parser extracts the context data from the raw source of a specific type and builds up the semantic tree of it. Widgets use their nodes as Context. Currently, there are three kinds of Parsers:

    * HTML Parser - parses the raw HTML. Every specific webpage needs its own Config.

    * BOS Parser - create context nodes from the props provided to B.O.S components. It is a built-in universal parser. Some contexts may require additional processing.

    * Microdata Parser - extract microdata metadata from HTML (experimental one).

    It is a built-in universal parser.

3.  _Rendering (simplified)_:

    a. For every Application in the active Mutation, load all Parsers and let them build a semantic tree from the raw source.

    b. For every context node in the semantic tree, for all Applications …

    * Resolve conditions for all targets using Context properties. Skip targets if conditions are not met.

    * For remaining targets, calculate the index (if any), and load InjectionLinks from storage.

    * Inject Widgets for all loaded injection links into the remaining targets.

## Delivery Plan: Release 1 - Pure B.O.S (Initial release)

### Objective:

Enable Mutable Web functionality (MVP) on pure B.O.S websites.

Fulfill the grant contract.

### Due date:

23.02.2024

### Artifacts:

1. **Demo**:

   [Near Social](https://social.dapplets.org/)

2. **PR-1: Pull Request for B.O.S-VM**.

   **Changes**:

   a. upgrade the dependency react-singleton-hook from ^3.1.1 to ^4.0.1,
   due to official React 18 support
   
   b. move dependency `styled-components` into `peerDependencies`, to reuse it in `mutable-web-engine` and `near-social-vm`.
   
   c. add parameter `skipTxConfirmationPopup`, switches off modal windows while sending transactions
   
   d. add parameter `enableComponentPropsDataKey`, controlling the props insertion into the HTML-attribute `data-props` as serialized JSON string. BosParser needs the attributes to parse Contexts.
   
   e. (BREAKING CHANGE) behavior change for the `enableComponentSrcDataKey` parameter. Now it inserts component names into HTML-attribute `data-component` only in the root level, not to all as before. BosParser requires these attributes for Context parsing.
   
   f. Bugfix: if `enableComponentSrcDataKey` is active, the attribute `data-component` got inserted into React-node`React.Fragment`, which is not visible in HTML-tree.
   
   (Warning: Invalid prop `data-component` passed to `React.Fragment`. React.Fragment can only have key and child props.)

4. **PR-2: Pull Request for B.O.S-Gateway**

   _Changes_ :

   a. add new dependency `mutable-web-engine` - the Mutable Web Engine.
   Starts on gateway start.
   
   b. add Mutation switch in the upper gateway panel.
   
   c. Features enableComponentPropsDataKey, enableComponentSrcDataKey activated for Parser.
   
   d. Webpack Config: deps `styled-components` and `near-social-vm` are reused in `mutable-web-engine`

   _To-do before merge_:

   1. Merge PR-1 for B.O.S-VM first!
   3. Replace the forked dep `near-social-vm` with the dep to the original B.O.S-VM after PR-1 applied.
   4. Switch off the flag `skipTxConfirmationPopup`, if needed

## Delivery Plan: Release 2 - add legacy web support

### Objective:

Enable the Mutable Web functionality (an MVP) on legacy websites.
Implement the B.O.S. Gateway as a browser extension

### Released on 14.03.2024:

14.03.2024 - released as [Mutable Web Extension](https://chromewebstore.google.com/detail/cnahdmdbhkphpbpbjjbfdnmbphbenglc) on Google Web Store.

## Delivery Plan: Release 3.x - Mutation management

### Objective:

Implement workflows for Mutation management.
Operations: create, delete, fork, publish, submit/accept of pull requests.
Using AstroDAO as the DAO management platform.

### Due date (preliminary):

08.04.2024 - release 3.1 (without AstroDAO functionality)
