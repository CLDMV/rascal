# Rascal — Initial Product & Architecture Design Brief

- **Status:** Initial Design / Discovery
- **Version:** 0.1
- **Project:** Rascal Media Platform
- **Purpose:** Establish the product vision, confirmed naming conventions, preliminary architecture, functional responsibilities, and outstanding design decisions before implementation.

## 1. Project Overview

Rascal is a modular, self-hosted media platform intended to replace and improve upon the functionality currently provided by Jellyfin, the *arr ecosystem, qBittorrent, Tdarr, and Jellyseerr.

Rather than requiring users to install, configure, connect, and maintain numerous independent applications, Rascal will consolidate their functionality into one integrated platform.

Rascal will provide the primary media server, media management engine, user interface, and extensibility framework.

Additional functionality will be installed through optional extensions and media-type manifests.

### Primary objectives

- Provide a complete, self-hosted media server with streaming and playback capabilities.
- Support movies, television, music, ebooks, and audiobooks.
- Eliminate duplicated functionality between existing media automation applications.
- Provide a modular extension architecture.
- Allow extensions to extend the core application or other extensions.
- Centralize configuration, monitoring, automation, and administration.
- Develop a native BitTorrent client rather than simply wrapping qBittorrent.
- Support optional media processing and transcoding workflows.
- Provide integrated media discovery, requests, and approval workflows.
- Support additional media types and capabilities without redesigning the core platform.
- Minimize unnecessary background services and duplicated application infrastructure.

The platform should be designed as a cohesive application rather than a collection of existing tools presented under one interface.

## 2. Confirmed Application Family

The initial naming convention uses the Ras prefix, derived from Rascal.

All ten initial names have been selected.

| Name | Component | Existing Equivalent | Implementation |
|---|---|---|---|
| Rascal | Core media platform | Jellyfin + shared *arr functionality | Main application |
| RasFlick | Movies | Radarr | Media manifest |
| RasBinge | Television | Sonarr | Media manifest |
| RasTune | Music | Lidarr | Media manifest |
| RasShelf | Books and audiobooks | Readarr / Audiobookshelf | Media manifest |
| RasSub | Subtitles | Bazarr | Functional extension |
| RasScout | Indexers and search providers | Prowlarr | Functional extension |
| RasFetch | BitTorrent | qBittorrent | Functional extension |
| RasForge | Media processing | Tdarr | Functional extension |
| RasQuest | Discovery and requests | Jellyseerr | Functional extension |

### Naming principles

- Rascal is the primary application and brand.
- Extensions follow the Ras prefix naming convention.
- Each extension may have its own icon and visual identity.
- The extension family should maintain a consistent overall design language.
- Extensions are components of Rascal, not necessarily independently deployed applications.
- Future extension names should be evaluated individually rather than forcing an unnatural naming convention.

**Naming status:** Confirmed for the initial family.

Visual branding, mascots, logos, colors, and iconography remain undecided.

## 3. Core Architecture

### 3.1 Rascal

Rascal itself is the media server and shared media management engine.

A separately named media engine extension is unnecessary.

The core application is expected to provide:

- Media library management
- Metadata and content identification
- Media scanning and indexing
- Media streaming and playback
- Playback transcoding
- User and permission management
- Shared media automation
- Monitoring and acquisition orchestration
- Media import and organization
- Extension management
- Application configuration
- APIs and extension communication
- Background jobs and event handling
- Centralized monitoring and administration

These are proposed functional responsibilities, not finalized internal component boundaries.

The design must still determine which capabilities belong in the minimal core and which should be implemented as optional internal modules.

### 3.2 Media-type manifests

RasFlick, RasBinge, RasTune, and RasShelf are intended to be primarily declarative manifests.

They define how Rascal handles a particular media type without implementing a separate media automation engine.

Examples of manifest-defined behavior:

- Content classifications
- Metadata and identification rules
- Search providers and search construction
- Content relationships
- Release and edition matching
- Monitoring requirements
- Quality preferences
- Acquisition rules
- File identification
- Import and organization behavior
- Applicable processing hooks

The shared Rascal engine executes the common workflows.

A manifest should not require an independent database, API server, scheduler, or download management engine.

However, the design must allow media-specific executable extensions when behavior cannot reasonably be represented declaratively.

**Open design question:** What belongs in a manifest versus an executable extension?

### 3.3 Functional extensions

Functional extensions provide distinct services and capabilities.

The initial functional extensions are:

- RasSub
- RasScout
- RasFetch
- RasForge
- RasQuest

These should use standardized interfaces and capabilities provided by Rascal.

Extensions must also be capable of extending other extensions or specific media types.

For example, RasSub should register subtitle capabilities for RasFlick and RasBinge without duplicating their media management functionality.

## 4. Media-Type Architecture

### 4.1 RasFlick — Movies

RasFlick defines movie-specific behavior.

Expected capabilities include:

- Movie identification
- Release years and editions
- Alternative cuts and versions
- Release monitoring
- Movie search and acquisition rules
- Quality and language preferences
- File matching and organization
- Metadata integration

### 4.2 RasBinge — Television

RasBinge defines television-specific behavior.

Expected capabilities include:

- Series identification
- Seasons and episodes
- Air dates and release monitoring
- Specials
- Episode numbering differences
- Multi-episode releases
- Season packs
- Episode upgrades
- Series metadata

Movies and television should share the same underlying automation machinery wherever possible.

The distinction is primarily their content models, relationships, and acquisition rules.

### 4.3 RasTune — Music

RasTune defines music-specific behavior.

Expected capabilities include:

- Artists and albums
- Tracks and releases
- Album editions
- Music metadata
- Audio formats
- Quality preferences
- Music acquisition and organization

**Open question:** How should Rascal represent albums, compilations, singles, multi-disc releases, and alternate editions without creating duplicated library entries?

### 4.4 RasShelf — Books and Audiobooks

RasShelf manages books and audiobooks as one unified media category.

**Confirmed principle:** Ebooks and audiobooks must not appear as unrelated libraries simply because their delivery formats differ.

A book may have:

- An ebook version
- An audiobook version
- Multiple ebook formats
- Multiple audiobook editions
- Different narrators or languages
- Multiple publication editions

These should be associated with the appropriate shared book identity.

A single book may offer both Read and Listen actions.

Expected capabilities include:

- Authors and series
- Editions
- Ebook reading
- Audiobook playback
- Narrator information
- Chapters
- Bookmarks
- Reading progress
- Listening progress
- Format management
- Metadata and acquisition

Open questions:

- How are different editions represented?
- Are reading and listening progress linked?
- Can users synchronize position between compatible ebook and audiobook editions?
- How should narrator and language variations appear?
- How should multi-volume books and collections be represented?

## 5. Functional Extension Responsibilities

### 5.1 RasScout — Indexers and Search

RasScout provides centralized search provider integration.

Expected responsibilities:

- Indexer configuration
- Authentication and connectivity
- Search execution
- Search result normalization
- Provider capability discovery
- Provider health monitoring
- Rate limiting
- Search result availability and filtering

RasScout searches configured sources but does not download the selected content.

Rascal and its media manifests determine which results are appropriate for acquisition.

### 5.2 RasFetch — BitTorrent

RasFetch is a native BitTorrent extension.

It is not intended to be a qBittorrent wrapper.

Expected responsibilities:

- Magnet links
- Torrent files
- Peer connections and transfers
- Download queues
- Data verification
- Bandwidth management
- Seeding
- Torrent lifecycle management
- Download progress and statistics
- Completion notifications
- Failure reporting

RasFetch does not determine the final media library destination.

It should remain usable as a BitTorrent client independently of RasFlick, RasBinge, or other media manifests.

**Confirmed principle:** RasFetch manages original downloaded data independently from Rascal's media import and processing workflow.

### 5.3 RasForge — Media Processing

RasForge provides configurable media processing comparable to Tdarr.

Potential capabilities:

- Video transcoding
- Audio transcoding
- Remuxing
- Codec conversion
- Container conversion
- Stream selection
- Audio track handling
- Subtitle processing
- Media validation
- Optimization workflows
- Hardware acceleration
- Distributed processing workers

RasForge should work against staged files, not original files being seeded.

Its processing responsibilities should remain separate from Rascal's playback-time transcoding.

**Open question:** Should RasForge initially use only local processing workers, or support remote workers from the beginning?

### 5.4 RasSub — Subtitles

RasSub provides subtitle management primarily for RasFlick and RasBinge.

Expected capabilities:

- Subtitle provider integration
- Language preferences
- Subtitle searching
- Release matching
- Subtitle downloading
- Subtitle synchronization
- Subtitle format handling
- Missing subtitle monitoring

RasSub should register its functionality with relevant media handlers rather than operating as an entirely independent media library.

### 5.5 RasQuest — Requests and Discovery

RasQuest provides media discovery and request workflows comparable to Jellyseerr.

Expected capabilities:

- Media discovery
- Search and recommendations
- User requests
- Request approvals
- Availability tracking
- Acquisition status
- Notifications
- Request history
- User permissions

RasQuest should support the media types installed in Rascal rather than being permanently limited to movies and television.

## 6. Acquisition and Processing Workflow

The following represents the agreed conceptual direction, not a finalized workflow implementation.

### 6.1 Proposed workflow

1. A user requests content through RasQuest, or Rascal detects missing/monitored content.
2. Rascal identifies the requested media and applicable media manifest.
3. RasScout searches configured providers.
4. Rascal evaluates results using the media manifest's acquisition rules.
5. RasFetch receives an approved BitTorrent job.
6. RasFetch downloads and verifies the content.
7. RasFetch reports completion to Rascal.
8. Rascal identifies the associated media item and import requirements.
9. Rascal copies the necessary files into a staging location.
10. RasForge optionally processes the staged files.
11. Rascal validates the resulting files.
12. Rascal imports the finalized media into its library.
13. The media becomes available for playback.
14. RasFetch continues managing seeding and original torrent data independently.

The same general workflow should accommodate other acquisition methods in the future.

### 6.2 Download completion

The distinction between download completion and seeding completion is important.

A torrent may be fully downloaded and verified while continuing to seed.

RasFetch must be capable of notifying Rascal that the content is ready for import without removing or modifying its original files.

Notifications should identify the corresponding download job, originating request, and available files.

The exact event contract is not yet defined.

### 6.3 Staging

**Confirmed principle:** When importing completed torrents, Rascal should copy required files into a separate staging location rather than hardlinking them.

Reasons include:

- Preserving original downloaded data
- Allowing uninterrupted seeding
- Supporting file transformations
- Isolating failed processing jobs
- Preventing unintended modification of source files

RasForge works on staged content.

The final import should only occur after the appropriate processing and validation stages have completed successfully.

### 6.4 Final import

Rascal is responsible for final library placement and registration.

The intended sequence is:

- Complete processing
- Validate outputs
- Determine final destination
- Finalize file placement
- Update library metadata
- Mark content available

Where possible, file finalization should be atomic.

The implementation must handle filesystems where staging and final libraries reside on different storage devices.

**Important:** The exact staging, import, movement, cleanup, and failure-recovery mechanisms remain open architectural decisions.

## 7. Storage and File Management — Design Not Finalized

The earlier example using `downloads/`, `staging/`, and `media/` was conceptual.

It should not be treated as the final filesystem design.

We need a more deliberate storage architecture before implementation.

### 7.1 Required logical storage areas

At minimum, the system must distinguish:

| Logical Area | Purpose |
|---|---|
| Application data | Configuration, databases, extension state |
| Download storage | Active and completed torrent data |
| Processing staging | Temporary working files |
| Processing output | Completed but not yet imported media |
| Media libraries | Final playable and readable content |
| Temporary cache | Transcoding and application cache |
| Metadata storage | Artwork, descriptions, generated assets |
| Logs / diagnostics | Troubleshooting and auditing |

These logical areas do not necessarily require separate physical folders or storage devices.

### 7.2 Open storage questions

#### Storage configuration

- Should Rascal manage storage paths automatically or allow full user-defined layouts?
- Should downloads, staging, and libraries use configurable storage pools?
- How should multiple disks, mounts, and network shares be handled?
- Should Rascal support independent storage locations per media library?
- Should users be able to configure storage priorities and free-space thresholds?
- How should Docker/container mount mappings be handled?

#### Staging and processing

- Should each acquisition create an isolated staging job directory?
- Should processing input and output be separate?
- Should staging use the same filesystem as the final media library when possible?
- What happens when staging space is insufficient?
- How should large files be copied without excessive temporary storage?
- Should intermediate processing files be retained after failures?
- How should failed or interrupted staging jobs recover?

#### Final media organization

- Should Rascal enforce a standard directory structure or support fully customizable naming?
- How should alternate movie editions be stored?
- How should multi-episode television files be represented?
- How should books with several formats be arranged physically?
- Should subtitles and metadata be stored alongside media or separately?
- How should existing manually organized libraries be imported?

#### File lifecycle

- When should downloaded originals be eligible for deletion?
- How should torrent retention and seeding policies interact with library management?
- How should duplicate files be identified?
- Should content hashes be maintained?
- How should partially completed imports recover after a crash?
- How should files be moved safely between filesystems?
- How should Rascal handle permissions and ownership?
- How should cleanup work after a failed transcode?

### 7.3 Storage design principle

Rascal should separate logical media ownership from physical file placement.

A media item should not depend on one particular directory structure.

The library database should understand the media, its editions, formats, file locations, and availability independently of how storage is mounted.

The storage architecture should be designed before settling on a default directory structure.

## 8. Extension System — Open Design Questions

The extension architecture is central to the entire platform.

Several major decisions still need to be established.

### 8.1 Extension packaging

- What does an extension package contain?
- How are extensions installed and updated?
- What is the manifest format?
- How are dependencies declared?
- How are extension capabilities registered?
- Can extensions include backend code, frontend components, or both?
- Can a manifest extend another manifest?
- Can extensions register hooks with other extensions?
- How are incompatible extension versions handled?

### 8.2 Execution model

- Do functional extensions execute in-process or in isolated workers?
- Should some extensions run in separate processes?
- What permissions should extensions have?
- Can extensions execute arbitrary code?
- What sandboxing is required?
- How are extensions started, stopped, and restarted?
- What happens when an extension crashes?
- Can extensions be disabled without corrupting their associated data?

### 8.3 Data management

- Do extensions share Rascal's database?
- Can extensions define their own schemas?
- How are schema migrations handled?
- How is extension data preserved during updates or removal?
- Who owns shared entities such as media items and downloads?

### 8.4 Events and APIs

- What is the internal event transport?
- Are events durable and replayable?
- How are retries handled?
- How are jobs correlated across extensions?
- How are duplicate events handled?
- What public APIs should extensions expose?
- How are capabilities discovered at runtime?

Extension boundaries and communication contracts should be established before developing the first functional extensions.

## 9. Media Data Model — Open Design Questions

A common media model is necessary to avoid duplicating existing *arr functionality.

The model must represent several levels of identity.

Examples include:

- A movie versus one of its editions
- A television series versus an episode
- An album versus an individual release
- A book versus its ebook or audiobook formats
- A logical media item versus its physical files

Questions to resolve include:

- What are Rascal's canonical media entities?
- How are external metadata identifiers associated with those entities?
- How are alternate editions represented?
- How is a requested item matched to an acquired release?
- How are duplicate releases handled?
- Can one logical media item have several available files?
- How do user playback and reading states relate to the media model?
- How should metadata changes be reconciled without losing user data?

This model should support media types beyond those included in the initial release.

## 10. Playback and Streaming — Open Design Questions

Rascal is intended to be a complete media server, but its playback architecture is not yet specified.

Areas requiring design include:

- Supported clients and platforms
- Browser playback
- Native and television applications
- Direct play versus transcoding
- Adaptive streaming
- Hardware transcoding
- Playback sessions
- Watch and reading progress
- Multiple user profiles
- Subtitle and audio track selection
- Remote streaming
- Device authentication
- Network bandwidth management
- Playback resume and synchronization

Playback-time transcoding should remain conceptually separate from RasForge's permanent library-processing workflows.

## 11. BitTorrent Engine — Open Design Questions

RasFetch is confirmed as a native BitTorrent extension.

However, the underlying implementation has not been selected.

Questions include:

- Build a BitTorrent engine from scratch or integrate an established engine library?
- Which engine libraries are suitable?
- Which BitTorrent protocol features are required?
- What network configuration is necessary?
- How should peer connections, bandwidth limits, and connection limits be managed?
- How should download priorities work?
- Should individual files inside torrents be selectable?
- How are torrent data locations managed?
- What seeding and retention policies are required?
- How should external download clients be supported, if at all?
- Can RasFetch operate independently of media automation?
- What happens when RasFetch is disabled while other media jobs are waiting?

RasFetch should have a clean service interface so media automation does not depend on the internal details of the torrent implementation.

## 12. RasForge — Processing Architecture Questions

RasForge will need a configurable processing pipeline.

The design needs to establish:

- Processing profiles
- Conditional processing
- Hardware acceleration
- Local versus remote workers
- Job queues and scheduling
- CPU/GPU resource limits
- Retry policies
- Output validation
- File and stream inspection
- Intermediate file handling
- Processing history and reporting
- Safe rollback or recovery
- Support for media-specific processing stages

RasForge should not be responsible for permanent library organization.

Its role is to receive an eligible staged job, produce validated output, and report the result to Rascal.

## 13. User Interface and Experience — Open Questions

The platform should present the extension system as one integrated application.

Users should not need to navigate between unrelated administrative interfaces.

Potential interface areas include:

- Home / Dashboard
- Media Library
- Discovery and Requests
- Downloads
- Processing
- Search Providers
- Extensions
- Users and Permissions
- Storage
- Settings
- System Health

Questions to resolve:

- Which sections appear when extensions are not installed?
- Can extensions register new navigation items and screens?
- Can extensions contribute controls to existing media detail pages?
- Should administrators and ordinary users have different interfaces?
- How should background activity be represented?
- How should the interface distinguish requested, downloading, processing, and available media?
- How should mobile and television experiences differ from desktop administration?

The extension architecture should support a consistent user experience rather than multiple unrelated applications inside one shell.

## 14. Additional Platform Decisions

The following cross-cutting areas also need initial design decisions.

### Authentication and permissions

- Local accounts
- External authentication
- Roles and permissions
- Request approvals
- Per-library access
- Extension-specific permissions

### Deployment

- Supported operating systems
- Container deployment
- Native installation
- CPU architectures
- GPU access
- Configuration management
- Backup and restoration

### Reliability

- Job persistence
- Restart recovery
- Event retries
- Database recovery
- File integrity
- Health checks
- Logging and diagnostics

### Security

- Extension permission boundaries
- API authentication
- Secret storage
- Network isolation
- Safe file operations
- Sandboxing untrusted extensions
- Update verification

### Performance

- Large library indexing
- Concurrent streaming
- Download and processing contention
- Background job priorities
- Storage I/O scheduling
- Memory and database efficiency

## 15. Confirmed Decisions vs. Open Design

| Area | Current Decision | Status |
|---|---|---|
| Main application name | Rascal | Confirmed |
| Extension family names | All nine names selected | Confirmed |
| Main architecture | One unified, extensible application | Confirmed direction |
| Media engine | Part of Rascal itself | Confirmed |
| Media types | Primarily declarative manifests | Confirmed direction |
| Movies and TV | Shared engine with specialized content rules | Confirmed direction |
| Books and audiobooks | Unified RasShelf library | Confirmed |
| BitTorrent | Separate native RasFetch extension | Confirmed |
| Torrent completion | Notifies Rascal of ready content | Confirmed |
| Torrent originals | Remain untouched during staging/processing | Confirmed |
| Import staging | Separate copy, not a hardlink | Confirmed |
| RasForge | Optional media processing extension | Confirmed |
| Final import | Managed by Rascal | Confirmed |
| Storage paths | Not established | Open |
| Folder layout | Not established | Open |
| Extension package format | Not established | Open |
| Execution isolation | Not established | Open |
| Database architecture | Not established | Open |
| Internal event system | Not established | Open |
| Canonical media data model | Not established | Open |
| BitTorrent implementation library | Not established | Open |
| Processing worker architecture | Not established | Open |
| Playback architecture | Not established | Open |
| Technology stack | Built on @cldmv/rummage | Confirmed |
| UI architecture | Not established | Open |
| Mascot and visual identity | Not established | Open |

## 16. Recommended Initial Design Work

Before implementation, create detailed design specifications for the following workstreams.

### Phase A — Platform Foundation

Define:

1. Supported deployment environments
2. How Rascal uses rummage (the technology base is @cldmv/rummage)
3. Core services and boundaries
4. Database architecture
5. Media data model
6. Extension framework
7. API and event contracts
8. Authentication and permissions

### Phase B — Storage and Media Lifecycle

Define:

1. Storage abstraction
2. Configurable storage locations
3. Download storage ownership
4. Staging and processing directories
5. File copy and movement semantics
6. Final import transactions
7. Recovery from failed operations
8. File retention and cleanup
9. Existing media library imports
10. Default and customizable organization layouts

### Phase C — Media Management

Define:

1. Media manifest specification
2. Metadata provider integrations
3. Content identification and matching
4. Monitoring rules
5. Quality and acquisition profiles
6. Search and selection logic
7. Media-type-specific behavior
8. Library scanning and availability

### Phase D — Functional Extension Specifications

Prepare separate specifications for:

1. RasScout
2. RasFetch
3. RasForge
4. RasSub
5. RasQuest

Each specification should define its responsibilities, interfaces, configuration, data ownership, lifecycle, failure behavior, and interactions with Rascal.

### Phase E — Playback and User Experience

Define:

1. Playback engine
2. Supported clients
3. Streaming protocols
4. User-facing libraries
5. Extension-driven UI
6. Administrator dashboard
7. Remote access
8. Playback synchronization
9. Visual identity and mascot system

These phases describe design workstreams, not a finalized implementation schedule. Some decisions will need to be made together because of their dependencies.

## 17. Design Principles

The following principles should guide future architectural decisions.

1. **One cohesive system.** Rascal should function as one integrated media platform rather than a wrapper around disconnected applications.
2. **Shared logic over duplication.** Common media automation behavior belongs in Rascal, not in multiple independently implemented extensions.
3. **Declarative where practical.** Media-type differences should be expressed through manifests whenever reasonably possible.
4. **Clear functional ownership.** Downloading, searching, processing, and library import should have distinct responsibilities.
5. **Preserve original data.** Downloaded torrent files must not be modified by library processing operations.
6. **Reliable file handling.** Interrupted downloads, failed processing, storage errors, and restarts must not result in silent data loss or partially imported media.
7. **Configurable storage.** The platform should not assume every installation has the same filesystem layout or storage topology.
8. **Extensibility from the beginning.** Third-party and future extensions should be supported by intentional interfaces rather than tightly coupled internal implementation details.
9. **One consistent user experience.** Extensions should integrate naturally into Rascal's interface.
10. **Avoid premature architecture commitments.** Conceptual workflows and example directory layouts should not be mistaken for finalized technical decisions.

## 18. Current Project Status

### Completed

- Selected Rascal as the primary application name.
- Established the Ras extension naming convention.
- Named all nine initial extensions and manifests.
- Established the unified media engine direction.
- Established a manifest-driven approach for media types.
- Unified ebooks and audiobooks under RasShelf.
- Established RasFetch as an independent BitTorrent extension.
- Established the basic completion-notification and staged-processing workflow.
- Established RasForge as an optional processing component.
- Established Rascal as the owner of final media imports.

### Outstanding

The project remains in the initial design stage.

Rascal is built on @cldmv/rummage, which provides the app shell, the extension host and the storage layer. No definitive extension API, data model, storage layout, playback architecture, or file lifecycle implementation has been selected.

**Next milestone:** Produce a technical architecture and design-decision document that resolves the foundational questions before implementation begins.

The immediate priorities should be the core media data model, extension contract, and storage/file lifecycle architecture, since these decisions will influence nearly every component of Rascal.
