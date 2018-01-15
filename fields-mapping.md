## Data Entities

The following table is applicable to all data entities that are migrated from Telerik Platform Backend Services to Kinvey. These are namely the Users, Data and Files content types. 

| BS field name              | Kinvey field name           | Description |
| -------------------------- | --------------------------- | ----------- |
| `Id`                       | `_id`                       |             |
| `CreatedAt`                | `_kmd.ect`                  |             |
| `ModifiedAt`               | `_kmd.lmt`                  |             |
| `Owner`                    | *`_acl.creator`             |             |
| `CreatedBy`                | *`CreatedBy`                |             |
| `ModifiedBy`               | *`ModifiedBy`               |             |
|                            |                             |             |
| `_ACL`                     | `_acl`                      |             |
| `_ACL.EveryoneCanRead`     | `_acl.gr`                   |             |
| `_ACL.EveryoneCanUpdate`   | *`_acl.gw`                  |             |
| `_ACL.EveryoneCanDelete`   | *`_acl.gw`                  |             |
| `_ACL.UsersCanRead`        | `_acl.r`                    |             |
| `_ACL.UsersCanUpdate`      | *`_acl.w`                   |             |
| `_ACL.UsersCanDelete`      | *`_acl.w`                   |             |
| `_ACL.RolesCanRead`        | `_acl.roles.r`              |             |
| `_ACL.RolesCanUpdate`      | `_acl.roles.u`              |             |
| `_ACL.RolesCanDelete`      | `_acl.roles.d`              |             |
|                            |                             |             |
| `MyCustomFieldName1`       | `MyCustomFieldName1`        |             |
| `FileRelationId-FieldName` | *`FileRelationId-FieldName` |             |

Specifics:

- If the value of the "Owner" of the item is empty GUID or `null`??? (e.g. `"00000000-0000-0000-0000-000000000000"`), the `_acl.creator` is set to the current KID. 

- The item is set to globally writeable only if both `EveryoneCanUpdate` and `EveryoneCanDelete` are `true`. 

- The item is allowed to be writeable only to these users from the `UsersCanUpdate` and `UsersCanDelete` arrays that are both allowed to Update and Delete the item. 

- All data items that contain a `File` relation field (as defined in the content type structure) will be migrated to the supported in Kinvey structure for handling file relations. Example:

  ```json
  // in Backend Services
  "PictureId": "my-file-id" 

  // in Kinvey 
  "PictureId": {"_type": "KinveyFile", "_id": "my-file-id"} 
  ```

  ​

## Files

| BS field name | Kiney field name | Description                            |
| ------------- | ---------------- | -------------------------------------- |
| `Filename`    | `_filename`      |                                        |
| `ContentType` | `mimeType`       |                                        |
| `Length`      | `size`           | The file size in bytes.                |
| `Uri`         | *N/A             | Not migrated.                          |
| `Storage`     | *N/A             | Not migrated.                          |
| N/A           | *`_public`       | Always set to `true`.                  |
| N/A           | *`_downloadURL`  | The physical contents of the resource. |

Specifics: 

- The `Uri` field that used to hold the path to the file resource on the Telerik Platform Backend Services content delivery network will not be migrated. The Kinvey file entity will have a `_downloadURL` field. Use this field to access the file contents. 
- The `Storage` system field in the file metadata is not migrated. 
- The migrated files to Kinvey will always be set to `_public` that means the link to the CDN will be visible to everyone with the link. This is for backwards-compatibiblity purposes. The developer can always update the file metadata and set the CDN link to not public. 
- Use the `_downloadURL` field of the Kinvey file entity to access the file contents. 

## Users

| BS field name      | Kinvey field name                        | Description                              |
| ------------------ | ---------------------------------------- | ---------------------------------------- |
| `Username`         | `username`                               | The username of the user chosen at the time of registration |
| `Email`            | `email`                                  | The user's email address required for uniqueness and email notificaitons related operations. |
| `IsVerified`       | `emailVerification.status`               | Migrated only in case the initial value is *true*. The status is set to `confirmed`. Not migrated if the user account is not verified. See the example below. |
| `IdentityProvider` | N/A                                      | Not migrated.                            |
| `Role`             | `_kmd.roles[{"roleId": "role-id-here"}]` | The identifier of the role the user is assigned to. |
| `VerificationCode` | N/A                                      | Not migrated.                            |

Specifics:

- `IsVerified`field for the user account in Backend Services is set to `true`, it is migrated to the following structure as supported in Kinvey

  ```json
  "emailVerification": {
      "status": "confirmed",
      "lastStateChangeAt": "2018-01-15T12:07:17.221Z",
      "lastConfirmedAt": "2018-01-15T12:07:17.221Z",
      "emailAddress": "user@example.com"
  }
  ```

  The `lastStateChangeAt` and `lastConfirmedAt` dates are set to the value of the `CreatedAt` field of the source entity. 

- The user account `Role` field will be migrated to the corresponding structure in Kinvey. The `grantDate` value is set to the moment in time when the user was assigned the role during the migration. 

  ```json
  "_kmd": {
  	// ommitted for brevity    
      "roles": [
          {
              "roleId": "8f489c82-6a9d-4318-8ebb-d581482c9275",
              "grantedBy": "kid_HJWVDV4MZ",
              "grantDate": "2018-01-12T15:14:35.110Z"
          }
      ]
  }
  ```

  ​

### Social Users

|            |                   |      |
| ---------- | ----------------- | ---- |
| `Identity` | `_socialIdentity` |      |
|            |                   |      |
|            |                   |      |
|            |                   |      |
|            |                   |      |
|            |                   |      |

