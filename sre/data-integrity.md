# Data Integrity

## Backups
1. Backups don't matter; what matters is recovery.
1. Setup script and alerts for testing backup integrity
1. Setup some way to test data integrity. To prevent the issue when data is slowly corrupted. Setup alert.
1. Use tired backup strategy
    ![Tired backup](./img/tired-backup.png)

## Protection methods
**Soft Deleteion** => **Lazy Deletion** => **Hard Deletion**
1. Soft deletion - objects are visible by clients in trash
1. Lazy deletion - objects are not visible to clients, but they are still not deleted for some time to prevent from the mistake of internal developer.
