# Delete Users

We will now add the functionality to delete users from the admin panel. In the `frontend/src/slices/usersApiSlice.js` file, add the following mutation:

```js
deleteUser: builder.mutation({
  query: (userId) => ({
    url: `${USERS_URL}/${userId}`,
    method: 'DELETE',
  }),
}),
```

Export it along with the rest of the functions:

```js
export const {
  useLoginMutation,
  useLogoutMutation,
  useRegisterMutation,
  useProfileMutation,
  useGetUsersQuery,
  useDeleteUserMutation,
} = usersApiSlice;
```

## User List Screen

In the `frontend/src/screens/admin/UserListScreen.js` file, import the new mutation and `toast` from `react-toastify`:

```js
import { toast } from 'react-toastify';
import {
  useDeleteUserMutation,
  useGetUsersQuery,
} from '../../slices/usersApiSlice';
```

Initialize it:

```js
const [deleteUser] = useDeleteUserMutation();
```

Add the delete handler:

```js
const deleteHandler = async (id) => {
  if (window.confirm('Are you sure')) {
    try {
      await deleteUser(id);
      refetch();
    } catch (err) {
      toast.error(err?.data?.message || err.error);
    }
  }
};
```

Now try and delete a user. The user should be removed. If there is an error, it should be displayed in a toast.
