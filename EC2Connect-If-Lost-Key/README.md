# Recovering Access to EC2 Windows Instance After Losing Key Pair

If you have lost the key pair for your EC2 Windows instance, you can regain access using one of the following solutions. These steps assume you are using EC2Config or EC2Launch to reset the administrator password.

## Solution 1: Create a New Instance from an AMI

1. Create an AMI from your existing instance.
2. Create a new key pair in AWS.
3. Launch a new EC2 instance using the created AMI, within the same VPC and Security Group, and assign the new key pair.
4. SSH into the new instance using the new key.

## Solution 2: Replace Key via Volume Attachment

1. Stop the original (locked) instance.
2. Detach the root volume from the original instance.
3. Attach the original volume to a temporary/new instance.
4. Use `lsblk` to list block devices.
5. Check file type with `lsblk -fp`.
6. Create a mount point: `sudo mkdir /mnt/cloudtech`
7. Mount the volume: `mount -o rw,nouuid /dev/xvdf1 /mnt/cloudtech`
8. List files: `ls /mnt/cloudtech`
9. Copy the `.ssh/authorized_keys` from the temp instance to the mounted volume:
   ```bash
   cat /home/ec2-user/.ssh/authorized_keys >> /mnt/cloudtech/home/ec2-user/.ssh/authorized_keys
   ```
10. Unmount the volume: `umount /mnt/cloudtech`
11. Detach the original root volume from the temp instance (stop the temp instance if needed).
12. Attach the original volume back to the original instance as the root volume (`/dev/xvda`).
13. Start the original instance and connect using the new key.
14. Once verified, you may terminate the temp instance if no longer needed.
