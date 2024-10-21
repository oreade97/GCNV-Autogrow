# GCNV-Autogrow
Autogrow Script for growing Google Cloud NetApp Volumes Storage Pools and Volumes
This script is designed to automatically manage the capacity of NetApp volumes and their associated storage pools in Google Cloud, using thresholds to prevent over-allocation and ensuring that the capacity stays within desired limits.

## Features
- Automated Volume Resizing: Automatically increases the capacity of a NetApp volume based on the percentage of used capacity.
- Storage Pool Auto-Expansion: If the available storage pool capacity is insufficient, the pool is expanded by the exact amount needed for the volume increase.
- State Check: Skips resizing operations if the incident state is marked as closed.
- Capacity Threshold: Resizes are only performed if the used volume capacity percentage exceeds a given threshold (80% in this case).
- Max Capacity Limit: Prevents storage pools from expanding beyond a maximum allowable size (default is 10 TiB).
- Logging: Logs details about the resize operations and capacity changes.

## How It Works
1) When a volume's capacity exceeds the threshold set by the Alerting Condition (default is 80%) Cloud monitoring creates an incident report
   *insert image here*
2) The function is triggered by a message from a Cloud Pub/Sub topic, which contains said incident report.
2) The function decodes the message and extracts relevant details, including the volume's region, name, and project ID.
3) It fetches the volume's current capacity and the associated storage pool's capacity from the GCNV API.
4) The function calculates whether the volume's used capacity exceeds a predefined threshold (80% of the total capacity).
5) If the threshold is met and the volume requires expansion, the volume is resized by a percentage (default is 20%). The function stops if:
6) If the volume size exceeds the storagepool size, the Storage Pool size is expanded by the volume capacity as well.
  a) The incident is closed.
  b) The volume size is now less than the threshold (80% by default)
  c) The required storage pool size exceeds the maximum limit (e.g., 10 TiB).
7) The function logs all operations, and both the volume and storage pool are updated if required.

## Prerequisites
Google Cloud account with the required permissions for managing NetApp volumes and storage pools.
Pub/Sub topic set up to trigger this Cloud Function based on volume capacity alerts.

## Variables
The following environment variables can be set to customize the function:

- capacity_to_increase: Percentage to increase the volume's capacity when resizing. Default is 20%.
- used_capacity_threshold: The percentage of used capacity below which the resize operation will be skipped. Default is 80%.
- max_storage_pool_capacity: The maximum allowed size for the storage pool. Default is 10 TiB.

## Installation & Deployment
1. Clone the Repository
2. Customize the Parameters
3. Run the script in Terraform
