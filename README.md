# react-permissions

Lightweight package that resolves permissions in your app

## How to use

Wrap your App in `PermissionsProvider`

```jsx
const Main = () => {
  return <PermissionsProvider>{/* your app code */}</PermissionsProvider>;
};
```

Provide `initialPermissions?: CheckResult` and/or `onCheckPermissions?: OnCheckPermissionsType`
If you have all permissions somewhere, and you don't need dynamic checks - use only `initialPermissions`

```jsx
type CheckResult = {
  action: string,
  allowed?: boolean,
}[];

type OnCheckPermissionsType = (
  actions: string[]
) => Promise<CheckResult> | CheckResult;

const exampleInitialPermissions = [
  { action: 'can_view_transactions', allowed: true },
  { action: 'can_edit_people', allowed: false },
];

const exampleCheckPermissions = (actions: string[]) => {
  return [
    { action: 'this_is_allowed', allowed: true },
    { action: 'denied_this_is', allowed: false },
    { action: 'undefined_is_allowed' },
  ];
};

return (
  <PermissionsProvider
    onCheckPermissions={exampleCheckPermissions}
    initialPermissions={exampleInitialPermissions}
  >
    // children
  </PermissionsProvider>
);
```

In the code itself use `PermissionCheck` component

```jsx
// Base usage:
return (
  <PermissionCheck action="can_view_files">
    <FileViewer />
  </PermissionCheck>
);

// Two or more permissions at the same time:
return (
  <PermissionCheck action={['can_view_files', 'is_system_admin']}>
    <SystemFileViewer />
  </PermissionCheck>
);

// Provide Fallback for denied access:
return (
  <PermissionCheck fallback="It's not for you, sorry" action="can_view_files">
    <SystemFileViewer />
  </PermissionCheck>
);

// Provide Loading if checks are dynamic:
return (
  <PermissionCheck loading={<Spin />} action="can_view_files">
    <SystemFileViewer />
  </PermissionCheck>
);

// Provide onDeny if you want to do something on permission deny
return (
  <PermissionCheck
    action="can_view_files"
    onDeny={action => {
      logger.info(`Action [${action}] got denied`);
      redirectUser('/');
    }}
  >
    <SystemFileViewer />
  </PermissionCheck>
);

// Provide custom logic for permissions
return (
  <PermissionCheck
    action={['can_view_files', 'can_view_system_files', 'has_full_access']}
    isAllowed={(allowed = [], denied = []) => {
      if (
        allowed.includes('can_view_files') &&
        allowed.includes('can_view_system_files')
      ) {
        return true;
      }

      return !denied.includes('has_full_access');
    }}
  >
    <FileViewer />
  </PermissionCheck>
);
```

## Utilities

### PermissionCheck

This component allows you to hide content based on permissions

```jsx
type PermissionCheckProps = {
  // this action/actions will be checked upon
  // if it's allowed - we will show content
  action: string | string[],

  // content that must be shown if action is allowed
  children: React.ReactNode,

  // content that must be shown if action is not allowed
  // default = null
  fallback?: React.ReactNode,

  // content that must be shown while we check if action is allowed
  // default = null
  loading?: React.ReactNode,

  // event when action is denied
  // fire alerts, write logs or redirects - do anything
  onDenied?: (action: string) => void,

  // if you need to implement custom check
  // by default we need to have all actions allowed
  isAllowed?: (allowedActions: string[], deniedActions: string[]) => boolean,
};

return <PermissionCheck>Secure Content</PermissionCheck>;
```

### useCheckPermission

Documentation TBD

### useCheckPermissions

Documentation TBD
