## Data Entities

The following table is applicable to all data entities that are migrated from Telerik Platform Backend Services to Kinvey. These are namely the Users, Data and Files content types. 

| Backend Services field name | Kinvey field name           | Value             | Description                              |
| --------------------------- | --------------------------- | ----------------- | ---------------------------------------- |
| `Id`                        | `_id`                       |                   | Unique ID of the item.                   |
| `CreatedAt`                 | `_kmd.ect`                  |                   | Date and time when the item was created, in ISO 8601 format. |
| `ModifiedAt`                | `_kmd.lmt`                  |                   | Date and time when the item was last modified, in ISO 8601 format. |
| `Owner`                     | *`_acl.creator`             |                   | ID of the user who holds the "Owner" role for the item, an empty GUID if the "create" operation was made with master priviliges. |
| `CreatedBy`                 | *`CreatedBy`                |                   | ID of the user who created the item, an empty GUID if the "create" operation was made with master priviliges. |
| `ModifiedBy`                | *`ModifiedBy`               |                   | ID of the last user who modified the item, an empty GUID if the operation was made with master priviliges. |
|                             |                             |                   |                                          |
| `_ACL`                      | `_acl`                      | Object            | An object that holds the definition of the entity-level permissions. |
| `_ACL.EveryoneCanRead`      | `_acl.gr`                   | Boolean           | Gives read permissions to all users      |
| `_ACL.EveryoneCanUpdate`    | *`_acl.gw`                  | Boolean           | Gives update permission to all user accounts. |
| `_ACL.EveryoneCanDelete`    | *`_acl.gw`                  | Boolean           | Gives delete permission to all user accounts. |
| `_ACL.UsersCanRead`         | `_acl.r`                    | Array of user IDs | Gives read permission to the specified user accounts. |
| `_ACL.UsersCanUpdate`       | *`_acl.w`                   | Array of user IDs | Gives update permission to the specified user accounts. |
| `_ACL.UsersCanDelete`       | *`_acl.w`                   | Array of user IDs | Gives delete permission to the specified user accounts. |
| `_ACL.RolesCanRead`         | `_acl.roles.r`              | Array of role IDs | Gives read permission to the specified security roles. |
| `_ACL.RolesCanUpdate`       | `_acl.roles.u`              | Array of role IDs | Gives update permission to the specified security roles. |
| `_ACL.RolesCanDelete`       | `_acl.roles.d`              | Array of role IDs | Gives delete permission to the specified security roles. |
|                             |                             |                   |                                          |
| `MyCustomFieldName1`        | `MyCustomFieldName1`        |                   | All custom fields are migrated to the Kinvey entity "as-is". |
| `FileRelationId-FieldName`  | *`FileRelationId-FieldName` | FileID            |                                          |
| `LocationFieldName`         | *`_geoloc`                  | Geo Point         | A definition of a geographic coordinate. |

Specifics:

- If the value of the "Owner" of the item is empty GUID (e.g. `"00000000-0000-0000-0000-000000000000"`), the `_acl.creator` is set to the current Kinvey App Key, for example, `kid_ABCDEF1234`. for consistency with the behavior in Kinvey. 

- The item is set to globally writeable (by setting the `_acl.gw` to `true`) only if both `EveryoneCanUpdate` and `EveryoneCanDelete` are `true`.  Otherwise set to `false`. 

- The item is allowed to be writeable (by setting the `_acl.w`  to `true`) only to these users from the `UsersCanUpdate` and `UsersCanDelete` arrays that are allowed to both *Update* and *Delete* the item. ???Otherwise???

- All data items that contain a `File` relation field (as defined in the content type structure in Backend Services) will be migrated to the supported in Kinvey structure for handling file relations. Example:

  ```json
  // in Backend Services
  "PictureId": "my-file-id" 

  // in Kinvey 
  "PictureId": {"_type": "KinveyFile", "_id": "my-file-id"} 
  ```

- All data items that contain a `GeoPoint` field (as defined in the content type strucure in Backend Services) will be migrated to the `_geoloc` field in the Kinvey entity which is the field designated to hold the geographical coordinates definition. If your data items have more than one field of type Geo Point, only the first one will be converted to the `_geoloc` field. The other fields will be migrated "as-is" and you will not be able to execute geoqueries on these fields. 

## Files

| Backend Services field name | Kinvey field name | Description                              |
| --------------------------- | ----------------- | ---------------------------------------- |
| `Filename`                  | `_filename`       | The file name set at the time of creating the file. |
| `ContentType`               | `mimeType`        | The Multipurpose Internet Mail Extensions (MIME) type of the file as specified at the time of creating the file. |
| `Length`                    | `size`            | An integer specifying the file size in bytes. |
| `Uri`                       | *N/A              | Not needed in Kinvey.  The Uniform Resource Locator (URL) referencing the physical contents of the resource on the Telerik Platform content delivery network (CDN). Example:`https://bs2.cdn.telerik.com/v1/abcdefg123456/fb40aef1-54c3-11e7-9021-04ac21c10e6` |
| `Storage`                   | *N/A              | Not needed in Kinvey.                    |
| N/A                         | *`_public`        | Always set to `true`.                    |
| N/A                         | *`_downloadURL`   | The Uniform Resource Locator (URL) referencing the physical contents of the resource on a Kinvey CDN. Example:`https://storage.googleapis.com/29e1ba7e957c4e7b9f6ce51819ae9835/ff6375ed-e1ec-3b72-681e-3660bb98b5b1/myawesomepicture.jpeg` |

Specifics: 

- The `Uri` field that used to hold the path to the file resource on the Telerik Platform Backend Services content delivery network will not be migrated. The Kinvey file entity will have a `_downloadURL` field. Use this field to access the file contents. 
- The `Storage` system field in the file metadata is not migrated. 
- The migrated files to Kinvey will always be set to `_public` that means the web address of the resource on the CDN will be visible to everyone that has the link. This is for backwards-compatibiblity purposes and to preserve the behavior from the Telerik Platform CDN. The developer can always update the file metadata and set the CDN link to not public. 
- Use the `_downloadURL` field of the Kinvey file entity to access the file contents. 

## Users

| Backend Services field name | Kinvey field name                        | Description                              |
| --------------------------- | ---------------------------------------- | ---------------------------------------- |
| `Username`                  | `username`                               | The username of the user chosen at the time of registration |
| `Email`                     | `email`                                  | The user's email address required for uniqueness and email notificaitons related operations. |
| `IsVerified`                | *`_kmd.emailVerification.status`         | Migrated only in case the initial value is *true*. The status is set to `confirmed`. Not migrated if the user account is not verified. Example: *See below* |
| `IdentityProvider`          | N/A                                      | Not migrated.                            |
| `Role`                      | *`_kmd.roles[{"roleId": "role-id-here"}]` | The identifier of the role the user is assigned to. |
| `VerificationCode`          | N/A                                      | Not migrated.                            |

Specifics:

- `IsVerified`field for the user account in Backend Services is set to `true`, it is migrated to the following structure as supported in Kinvey

  ```json
  {
    // ommitted for brevity
     "_kmd":{
        "lmt":"2018-01-04T10:20:27.743Z",
        "ect":"2018-01-04T10:20:27.743Z",
        "emailVerification":{
           "status":"confirmed",
           "lastStateChangeAt":"2018-01-08T12:38:06.874Z",
           "lastConfirmedAt":"2018-01-08T12:38:06.874Z",
           "emailAddress":"adobrev@progress.com"
        }
     }
    // ommitted for brevity
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

| Backend Services field name | Kinvey field name                  | Description                              |
| --------------------------- | ---------------------------------- | ---------------------------------------- |
| `Identity`                  | * `_socialIdentity`                | The object containing the definition of the related third-party accounts information of the user. |
| `Identity.<provider-name>`  | *`_socialIdentity.<provider-name>` | The specific information for the source third-party provider. Example: `_socialIdentity.facebook` |
| `Id`                        | *`_acl.creator`                    | ID of the user account.                  |
|                             |                                    |                                          |
|                             |                                    |                                          |
|                             |                                    |                                          |

Specifics:

- The `_socialIdentity` field in Kinvey holds the corresponding social-account user information. Here is how the initial user account in Backend Services will look in Telerik Platform:

  ```json
  {
     "IsVerified":true,
     "IdentityProvider":"Facebook",
     "Identity":{
        "Facebook":{
           "id":"111111111111111",
           "first_name":"First",
           "last_name":"Last",
           "age_range":{
              "min":21
           },
           "link":"https://www.facebook.com/........",
           "gender":"male",
           "locale":"en_US",
           "picture":{...},
           "timezone":3,
           "updated_time":"2016-10-26T13:27:16+0000",
           "verified":true,
           "email":"first.last@example.com",
           "name":"First Last"
        }
     },
     "Email":"first.last@example.com",
     "DisplayName":"First Last",
     ...
     "Id":"afeea6b0-e009-11e7-9b66-3b0188bc0ae6"
  }
  ```

  and after it is migrated to Kinvey:

  ```json
  {
     "_kmd":{
        "lmt":"2018-01-04T10:20:27.743Z",
        "ect":"2018-01-04T10:20:27.743Z",
        "emailVerification":{
           "status":"confirmed",
           "lastStateChangeAt":"2018-01-08T12:38:06.874Z",
           "lastConfirmedAt":"2018-01-08T12:38:06.874Z",
           "emailAddress":"first.last@example.com"
        }
     },
     "roles":[
        {
           ...
        }
     ],
     "_socialIdentity":{
        "facebook":{
           "id":"111111111111111",
           "first_name":"First",
           "last_name":"Last",
           "age_range":{
              "min":21
           },
           "link":"https://www.facebook.com/........",
           "gender":"male",
           "locale":"en_US",
           "picture":{
              ...
           },
           "timezone":3,
           "updated_time":"2016-10-26T13:27:16+0000",
           "verified":true,
           "email":"first.last@example.com",
           "name":"First Last"
        }
     },
     "email":"first.last@example.com",
     "DisplayName":"First Last",
     "_acl":{
        "creator":"afeea6b0-e009-11e7-9b66-3b0188bc0ae6"
     },
     ...
     "_id":"afeea6b0-e009-11e7-9b66-3b0188bc0ae6"
  }
  ```

  ​

- The `_acl.creator` value of the user account created from social provider is always set to the item `Id` (as opposed by the other entities where this is set to the `Owner` of the item.)
