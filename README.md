Rotating Leadership DAO 

Overview

The Rotating Leadership DAO is a Clarity smart contract designed to manage decentralized organizational leadership in a predictable, automated cycle.
Leadership rotates among registered DAO members every fixed number of blocks (default: 144 blocks ≈ 24 hours on Stacks). The currently assigned leader has special permissions, such as adding/removing members and forcing an early rotation when necessary.

This contract is ideal for DAOs, cooperatives, working groups, or automated governance structures requiring non-permanent, algorithmic leadership.

Key Features
✔ Automated Leadership Rotation

Leadership changes automatically when a rotation is due.

Rotation can also be triggered manually.

Fallback errors prevent rotation when no members exist.

✔ Leader-Controlled Membership

Only the current leader can:

Add new DAO members

Remove existing DAO members

✔ Rotation Timing Logic

Rotation occurs every BLOCKS-PER-ROTATION blocks (default: 144).

Anyone can call rotate-leadership once the period has elapsed.

Leader can artificially set the rotation as "due" using force-rotate.

✔ Full Member Indexing

Members are tracked by both:

A membership map

A numerically indexed list (used for round-robin rotation)

Contract Constants
Constant	Description
CONTRACT-OWNER	The deploying address (initial leader).
BLOCKS-PER-ROTATION	Minimum blocks between leadership rotations.
Error codes	Standardized error responses (e.g., not authorized, not a member, rotation invalid).
Data Variables
Variable	Type	Purpose
current-leader	principal	Current DAO leader
last-rotation-block	uint	Block height of last rotation
member-count	uint	Number of registered members
rotation-index	uint	Round-robin index for selecting next leader
Data Maps
Map	Key → Value	Purpose
members	principal → bool	Tracks membership status
member-list	uint → principal	Round-robin leadership list
proposal-votes	uint → {...}	(Reserved for governance expansion)
voter-record	{proposal-id, voter} → bool	(Reserved for voting logic expansion)
Public Functions
1. Membership Management
Function	Description
add-member(principal)	Adds a new member — only leader can call
remove-member(principal)	Removes a member — only leader can call
2. Leadership Rotation
Function	Description
rotate-leadership()	Performs a round-robin rotation if due
force-rotate()	Leader forces the rotation window to open
Read-Only Functions
Function	Returns	Description
get-current-leader	(ok principal)	Current leader
get-blocks-until-rotation	(ok uint)	Blocks remaining until next rotation
is-member(account)	bool	Checks membership
get-member-count	(ok uint)	Number of members
is-rotation-due	bool	Indicates if rotation is allowed
get-next-leader	(ok principal)	Who the next leader will be
Initialization

Upon deployment:

Deployer becomes first member and initial leader

member-count = 1

rotation-index = 0

last-rotation-block = block-height

How Rotation Works

Members are stored in an indexed list

On rotation:

rotation-index = (rotation-index + 1) mod member-count

current-leader becomes the member at the new index

last-rotation-block = block-height

This ensures predictable, fair, round-robin leadership.

Security Considerations

Only the current leader may modify membership.

Leadership cannot rotate if there are zero members.

Unauthorized rotate attempts are rejected.

Member removal does not reindex the list (ensuring deterministic rotation).