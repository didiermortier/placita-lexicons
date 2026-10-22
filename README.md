# Placita lexicons

Lexicon schemas for [Placita](https://placita.app), a place-first app on the AT Protocol. People save places, add tags and notes, and choose which places are public. Every record lives in the user's own repository.

This repository contains the schemas only. The Placita app source is private.

## Records

| NSID | Record key | What it holds |
|---|---|---|
| `app.placita.place` | `tid` | One saved place: name, location, status (`want` or `been`), visibility, optional OpenStreetMap link and copy, optional place code, optional rating, note, photos (with an optional photo credit), tags, and the address of the Bluesky post it was shared as |
| `app.placita.tag` | `any` (the slug) | A personal tag. Places point to tags with a `com.atproto.repo.strongRef` |
| `app.placita.collection` | `any` (the tag's slug) | A tag shared as a public collection: the record existing means the tag is shared publicly. Holds an optional description; the public page lists only the author's Public places that carry the tag |
| `app.placita.profile` | `literal:self` | The user's city-level home location, and an optional list of up to 8 profile links (https address and optional name) |
| `app.placita.authors` | `literal:self` | The Placita account's list of known Explore authors: the accounts whose Public places guests see in Explore and on the shared place pages. Kept by the Placita account; readable by anyone |
| `app.placita.guide` | `tid` | A city guide made of sections that reference public place records |

## Things to know when you read these records

- **Coordinates are strings.** The AT Protocol data model has no floats. Locations use `community.lexicon.location.geo` (latitude and longitude as strings) or `community.lexicon.location.address`.
- **`visibility` is a display hint.** `public` means Placita may show the place in discovery. `hidden` only means Placita does not show it. Records in an AT Protocol repository are readable by anyone, including hidden ones.
- **OpenStreetMap link.** `osmType` (`node`, `way` or `relation`) and `osmId` identify the linked map feature. They are optional. Many places have no link.
- **Place code.** `placeCode` is an opaque random code for a place that has no OpenStreetMap link. The person who first saves such a place makes it, and every copy that another person saves keeps it unchanged, so copies of one place can be found together. A place with an OpenStreetMap link does not need one.
- **Photo credit.** `photoCredit` is free text such as "Photos by @alice.example" that says who took the uploaded photos when they are not the owner of the record, for example after a copy. It only appears together with `photos`.
- **Shared post.** `postRef` is a strong reference to the Bluesky post the owner shared this place as, when they shared it from the Placita app. The post carries a link card for the owner's own page for the place. Absent when the place was never shared.
- **Rating.** `rating` is a whole number from 1 to 5 and only appears on places with status `been`. No rating means not rated.
- **OpenStreetMap copy.** `osm` holds what OpenStreetMap knew when the place was linked: a structured `address`, the raw `tags` (key and value, at most 60) and `checkedAt`. It is a copy, not a live view, so it can be out of date or wrong, and the user's own fields come first. Data (c) OpenStreetMap contributors, under the [Open Database Licence](https://opendatacommons.org/licenses/odbl/).
- **Photos** are blobs (up to 4, image only). A place can also carry a source photo with its author and licence fields.
- **Profile links.** `links` is the person's own list of buttons for their profile page, in display order. When the field is missing, apps show the person's Bluesky and mu.social profiles by default. When it is present it is the whole list, so an empty list means the person chose to show none. Each link has an https `url` and an optional `title`. Clients should only open https addresses.
- **Tags** are strong references. A tag edit changes the tag record's CID, so Placita updates the places that point to it in the same write.

## Stability

Once published, these schemas change by adding optional fields only. Fields are not removed or retyped. Clients should keep fields they do not know when they edit a record.

## Publication status

Published on 2026-10-09. The DNS record `_lexicon.placita.app` names `did:plc:sg5d3buklhrsufaej4n4hjzx` (the `placita.app` account) as the authority for `app.placita.*`. The schemas are `com.atproto.lexicon.schema` records in that account's repository, with the NSID as record key, for example `at://did:plc:sg5d3buklhrsufaej4n4hjzx/com.atproto.lexicon.schema/app.placita.place`. Tools that resolve lexicons by NSID (for example `@atproto/lex-resolver`) find them there. The files in this repository are the source of the published records.

Updated on 2026-10-13: `app.placita.place` gained the optional `rating` and `osm` fields (with the `osmDetails`, `osmAddress` and `osmTag` definitions). Nothing was removed or changed.

Updated on 2026-10-14: `app.placita.place` gained the optional `editedAt` on the `osm` object, which says when the person last changed those details by hand or typed them (details with no `checkedAt` were never copied from OpenStreetMap). Descriptions for `osm` and `checkedAt` were made clearer. Nothing was removed or changed.

Updated on 2026-10-14 (second change): `app.placita.place` gained the optional `placeCode` and `photoCredit` fields. Nothing was removed or changed.

Updated on 2026-10-16: `app.placita.place` gained the optional `postRef` field, a strong reference to the Bluesky post a place was shared as (set when its owner shares the place from Placita). Nothing was removed or changed.

Updated on 2026-10-17: a new schema, `app.placita.authors`, was published: the Placita account's list of known Explore authors, held in the Placita account's own repository and readable by anyone. Nothing was removed or changed in the existing schemas.

Updated on 2026-10-22: a new schema, `app.placita.collection`, was published: a tag shared as a public collection, one record per shared tag, keyed by the tag's slug. Nothing was removed or changed in the existing schemas.

## Licence

MIT. See [LICENSE](LICENSE).

Updated on 2026-10-22: `app.placita.profile` gained the optional `links` field (with the `link` definition). Nothing was removed or changed. The network copy of the schema is written separately; until then this file is ahead of the published record.
