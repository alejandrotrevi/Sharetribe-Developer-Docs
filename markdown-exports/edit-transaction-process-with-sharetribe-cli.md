---
title: "Edit transaction process with Sharetribe CLI"
source: "https://www.sharetribe.com/docs/how-to/edit-transaction-process-with-sharetribe-cli/"
extracted_date: "2025-01-27"
summary: "This tutorial shows you how to edit transaction process with Sharetribe CLI. You will learn how to pull process definition, make a small change to it and push the change."
---

- [Pull existing process](#pull-existing-process)
- [A word about edn format](#a-word-about-edn-format)
- [Extend the review period](#extend-the-review-period)
- [Validate and push the process](#validate-and-push-the-process)
- [Update alias](#update-alias)
- [Summary](#summary)

# Edit transaction process with Sharetribe CLI

This tutorial shows you how to edit transaction process with Sharetribe CLI. You will learn how to pull process definition, make a small change to it and push the change.

**Required skills:** basic command line, text editing

Sharetribe CLI (Command-line interface) is a tool for changing your marketplace's configurations such as transaction processes and email templates.

This tutorial expects that you have already installed Sharetribe CLI and are logged in with your API key. It's recommended to first read the tutorial [Getting started with Sharetribe CLI](/docs/introduction/getting-started-with-sharetribe-cli/). If you haven't read [how transaction processes work in Sharetribe](/docs/concepts/transaction-process/), it's a good idea to do that before starting this tutorial.

In this tutorial we extend the marketplace review period. After we've made the change, we'll push the updated transaction process version and update the existing alias. If you want to create a new transaction process based on an existing one instead, you can follow these [instructions to create a new transaction process and alias](/docs/tutorial/create-transaction-process/#create-a-new-process)

Let's get started!

## Pull existing process

The first thing to do is to list all the existing processes with CLI command `process list`.

Remember to include your marketplace ident to the command with the `--marketplace <marketplace ident here>` options or the short version `-m <your marketplace ident here>`:

```bash
flex-cli process list -m my-marketplace-dev
```

From the list of processes, pick the one that you want to edit. In this tutorial we'll use process `default-booking`, version 1. You might have different processes in your marketplace, so you can use those. However, you can check the content of this default booking example process online from [Sharetribe example processes](https://github.com/sharetribe/example-processes) Github repository.

We can pull the process with the `process pull` command. Let's see the options that the command needs:

```bash
flex-cli help process pull
```

We can see that required options are:

- `--path` the path where the process is saved
- `--process` name of the process
- `--version` or `--alias`. Process version or alias pointing to the version that we want to pull
- `--marketplace` the marketplace

Pull the process and save it to `process` directory:

```bash
flex-cli process pull --process default-booking --version 1 --path process -m my-marketplace-dev
```

See what's inside the `process` directory:

```bash
ls process
```

(Windows users: use `dir` instead of `ls`)

You can see that there are two items in the directory:

- `process.edn` file, which defines the transaction process
- `templates` directory, which contains all the transaction email templates for this process

Next, we're going to edit the process description, but before that...

## A word about edn format

The process description uses a format called **edn**. At first glance it may seem a bit odd if you haven't seen edn before, but fear not! On closer look you'll recognize many similarities with JSON format.

Here's a small example of edn:

```clojure
;; This is a comment. Comments in edn start with ";;"
;;

{:number 1 ;; a number, for example `1`, `2.2`, `-500`, `1.23456M`
           ;; (where `M` denotes that exact precision is desired)
 :string "This is a string"
 :boolean true ;; or false
 :keyword :this-is-a-keyword
 :namespaced-keyword :namespaced/keyword
 :vector [1, "abc", false] ;; same as "array" in JSON
 :map {:first-name "John",
       :last-name "Doe",
       :age 55} ;; same as "object" in JSON
}
```

Keywords are used heavily in the process description syntax as keys in maps as well as enum values. Keywords start with a `:` but are otherwise similar to strings. Keywords can have a namespace, in which case they are called qualified keywords, or be plain (unqualified). The part before `/` is the namespace. So for example, `:actor.role/customer` is a keyword in the namespace `actor.role`.

Commas (`,`) in edn are optional and often omitted, but can be used for clarity.

Now that you know the basics of the edn format, let's edit the `process.edn` file!

## Extend the review period

Open the `process.edn` in your favorite editor.

_(To get proper syntax highlighing, you may need to install a plugin to your editor. edn is subset of Clojure, so a Clojure plugin will give you proper edn highlighting.)_

From the `process.edn` file, you'll find a map with a key `:transitions`. The value of the `:transitions` key is a vector of transition in your marketplace. Each transition contains values for keys like `:name`, `:actor`, `:actions`, `:to` and `:from`.

To extend the review period in the transaction process, we need to find the transition where the review periods are defined. In the Sharetribe default processes, the review period length is defined in the transition `:transition/expire-review-period`, and if one participant has already reviewed the other, in either `:transition/expire-provider-review-period` or `:transition/expire-customer-review-period`. By default, the review periods are defined as 7 days.

When you have found the transitions, change the value of the `:fn/period` in the `:at` time expression to `["P10D"]` to extend the review period to 10 days.

```diff
 {:format :v3
  :transitions
  [{:name :transition/inquire,
    ...
  {:name :transition/expire-review-period,
   :at
   {:fn/plus
-    [{:fn/timepoint [:time/booking-end]} {:fn/period ["P7D"]}]},
+    [{:fn/timepoint [:time/booking-end]} {:fn/period ["P10D"]}]},
   :actions [],
   :from :state/delivered,
   :to :state/reviewed}
  {:name :transition/expire-provider-review-period,
   :at
   {:fn/plus
-    [{:fn/timepoint [:time/booking-end]} {:fn/period ["P7D"]}]},
+    [{:fn/timepoint [:time/booking-end]} {:fn/period ["P10D"]}]},
   :actions [{:name :action/publish-reviews}],
   :from :state/reviewed-by-customer,
   :to :state/reviewed}
  {:name :transition/expire-customer-review-period,
   :at
   {:fn/plus
-    [{:fn/timepoint [:time/booking-end]} {:fn/period ["P7D"]}]},
+    [{:fn/timepoint [:time/booking-end]} {:fn/period ["P10D"]}]},
   :actions [{:name :action/publish-reviews}],
   :from :state/reviewed-by-provider,
   :to :state/reviewed}
```

Save the changes you've made to `process.edn` file.

## Validate and push the process

After each modification to the `process.edn` file, it's good idea to validate that the changes are correct. The CLI command `process` can do this:

```bash
flex-cli process --path process
```

In case the `process.edn` file is valid, you'll see a description of your process with all the states, transitions and notifications listed. In case the file is invalid, you'll see a validation error.

Now that we have validated the `process.edn` file we are ready to push the changes to