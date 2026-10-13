# Placita lexicons

Lexicon schemas for [Placita](https://placita.app), a place-first app on the AT Protocol. People save places, add tags and notes, and choose which places are public. Every record lives in the user's own repository.

This repository contains the schemas only. The Placita app source is private.

## Records

| NSID | Record key | What it holds |
|---|---|---|
| `app.placita.place` | `tid` | One saved place: name, location, status (`want` or `been`), visibility, optional OpenStreetMap link and copy, optional rating, note, photos and tags |
| `app.placita.tag` | `any` (the slug) | A personal tag. Places point to tags with a `com.atproto.repo.strongRef` |
| `app.placita.profile` | `literal:self` | The user's city-level home location |
| `app.placita.guide` | `tid` | A city guide made of sections that reference public place records |

## Things to know when you read these records

- **Coordinates are strings.** The AT Protocol data model has no floats. Locations use `community.lexicon.location.geo` (latitude and longitude as strings) or `community.lexicon.location.address`.
- **`visibility` is a display hint.** `public` means Placita may show the place in discovery. `hidden` only means Placita does not show it. Records in an AT Protocol repository are readable by anyone, including hidden ones.
- **OpenStreetMap link.** `osmType` (`node`, `way` or `relation`) and `osmId` identify the linked map feature. They are optional. Many places have no link.
- **Rating.** `rating` is a whole number from 1 to 5 and only appears on places with status `been`. No rating means not rated.
- **OpenStreetMap copy.** `osm` holds what OpenStreetMap knew when the place was linked: a structured `address`, the raw `tags` (key and value, at most 60) and `checkedAt`. It is a copy, not a live view, so it can be out of date or wrong, and the user's own fields come first. Data (c) OpenStreetMap contributors, under the [Open Database Licence](https://opendatacommons.org/licenses/odbl/).
- **Photos** are blobs (up to 4, image only). A place can also carry a source photo with its author and licence fields.
- **Tags** are strong references. A tag edit changes the tag record's CID, so Placita updates the places that point to it in the same write.

## Stability

Once published, these schemas change by adding optional fields only. Fields are not removed or retyped. Clients should keep fields they do not know when they edit a record.

## Publication status

Published on 2026-10-09. The DNS record `_lexicon.placita.app` names `did:plc:sg5d3buklhrsufaej4n4hjzx` (the `placita.app` account) as the authority for `app.placita.*`. The schemas are `com.atproto.lexicon.schema` records in that account's repository, with the NSID as record key, for example `at://did:plc:sg5d3buklhrsufaej4n4hjzx/com.atproto.lexicon.schema/app.placita.place`. Tools that resolve lexicons by NSID (for example `@atproto/lex-resolver`) find them there. The files in this repository are the source of the published records.

Updated on 2026-10-13: `app.placita.place` gained the optional `rating` and `osm` fields (with the `osmDetails`, `osmAddress` and `osmTag` definitions). Nothing was removed or changed.

Updated on 2026-10-14: `app.placita.place` gained the optional `editedAt` on the `osm` object, which says when the person last changed those details by hand or typed them (details with no `checkedAt` were never copied from OpenStreetMap). Descriptions for `osm` and `checkedAt` were made clearer. Nothing was removed or changed.

## Licence

MIT. See [LICENSE](LICENSE).
