# AWS Virtual Private Cloud

A VPC is a virtual network inside AWS. A VPC is within 1 account & 1 Region.

- VPC is Private & Isolated unless you decide otherwise.
- Services deployed in the same VPC can communicate, but outside connections are blocked by default.
- Default VPC (maximum 1 per region) and custom VPC are available.
- Default VPC is public by default and is less flexible than custom VPC.
- Default VPC CIDR: `172.31.0.0/26`