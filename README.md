# arXiv Authors

## Problem & Goals

At submission time, submitters are asked to provide a single string with the
names of all of the authors of a paper. This string observes a canonical
format, described here. That format is not strictly enforced, but is generally
sufficient to individuate author names and affiliations as entered. That author
name string is stored in the core metadata record for the paper version. In
addition, relation in the submission database establish the identity of the
original submitter, and additional “author-owners” who have claimed the paper.

The classic arXiv system does provide an “author authority record,” which is
essentially just an URI for an arXiv user. This is tied to arXiv user
accounts, and not independently related to papers. The same is true for ORCID
identifiers, which can be added to arXiv user profiles.

In many cases, the number of author-owners will be smaller than the number of
author names in the metadata record. In addition, there is no reliable way to
relate known author-owners to names parsed from the metadata record. 

As a result, we can't do some things that users want. We want to provide
features like:

  - Click on an individual name on the paper abstract page to see other papers
    by that author (and not by other authors with the same or similar name).
  - Limit searches by author identities, rather than relying on imprecise text
    matching.
  - Authors can see and share a list of their papers, even if they have not
    explicitly claimed those papers in their arXiv accounts.

This project provides the core data structure and API that can power these
features in the submission UI, search, and other parts of the system.


## Requirements

1. Store relationships between individual author names in the canonical author
   name string and stable name authority records. The "individual author name"
   can just be a substring (e.g. start and end character offsets) of the
   canonical author string. Note that it is possible for substrings to overlap.

2. Wherever possible, we should avoid creating our own authority records. We
   should leverage ORCID, INSPIRE, and other platforms that have done the hard
   work of curating author identities.

3. Must provide a RESTful JSON API that supports the following operations:

  - Add relation between a fragement of the e-print canonical author string
    and an author authority record.
  - Retrieve all such relations for a specific e-print.
  - Retrieve all such relations for a specific author authority record.

4. Must provide a UI that allows author-owners to view and edit relations
   between author names and authority records, and approve/reject proposed 
   relations from non-author-owners (below).

5. Non-author-owners should be able to submit a proposed relation, that can be
   approved by an author-owner. 
   
  - E.g. if a co-author from a large collaboration (so unlikely to be an
    author-owner in the system) wants to be sure that an e-print is associated
    with their identity.

6. Partner platforms should be able to submit relations based on their 
   curatorial work and other data. E.g. ADS, INSPIRE, others. For trusted
   platforms, these relations should be treated as true unless an author-owner
   specifies otherwise.

Once the core functionality in place, we will want to do things like:

7. Emit system events (via Kinesis) about new authorship information.

8. Propose possible relations to author-owners based on automated process
   (e.g. graph-based learning).


## Constraints

1. Flask app that follows the design approach outlined in
   https://arxiv.github.io/arxiv-arxitecture/crosscutting/services.html . Can
   be deployed as a Docker image, e.g with uWSGI application server
2. Separate blueprints for API, user interface
3. Use [arXiv base](https://github.com/arXiv/arxiv-base) for base templates,
   error handling, etc
4. Use [arXiv auth](https://github.com/arXiv/arxiv-auth) for authn/z.
5. API documented with OpenAPI 3 and JSON schema.

## Quick-start

We use [Pipenv](https://github.com/pypa/pipenv) for dependency management.

```bash
pipenv install --dev
```

You can run either the API or the UI using the Flask development server.

```bash
FLASK_APP=ui.py FLASK_DEBUG=1 pipenv run flask run
```

Dockerfiles are also provided in the root of this repository. These use uWSGI
and the corresponding ``wsgi_[xxx].py`` entrypoints.

## Contributing

Please see the [arXiv contributor
guidelines](https://github.com/arXiv/.github/blob/master/CONTRIBUTING.md) for
tips on getting started.

## Code of Conduct

All contributors are expected to adhere to the [arXiv Code of
Conduct](https://arxiv.org/help/policies/code_of_conduct).