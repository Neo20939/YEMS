# Super Admin Portal – YEMS

## Overview

The Super Admin Portal provides platform-wide oversight and management capabilities for Yeshua Educational Management System (YEMS).

## Access

### URL
- **Super Admin Portal:** `superadmin.html`
- Direct access: Open `superadmin.html` in your browser

### Default Credentials
| Role | Email | Password |
|------|-------|----------|
| Super Admin | `superadmin@yeshua.edu` | `superadmin` |

## Features

### 1. Dashboard (`superadmin-home`)
- Platform-wide statistics overview
- Total users, admins, institutions
- System health status
- Quick action cards
- Recent activity feed

### 2. Admin Management (`superadmin-admins`)
- View all platform administrators
- Create new admin accounts
- Edit admin details
- Suspend/activate admins
- Delete admin accounts
- View admin roles (Super Admin vs Admin)

### 3. Institutions Management (`superadmin-institutions`)
- View all connected schools/institutions
- Add new institutions
- Edit institution details
- View institution statistics (students, teachers)
- Activate/deactivate institutions
- Delete institutions

### 4. System Health (`superadmin-system-health`)
- Real-time system status monitoring
- API connectivity status
- Database connection status
- Storage usage
- System uptime
- Active sessions
- Platform version info
- Environment details

### 5. RBAC Policies (`superadmin-rbac`)
- View all system roles
- Create custom roles
- Edit role permissions
- Delete roles
- Assign users to roles
- Permission matrix management

**Default Roles:**
- **Super Admin** 👑 – Full platform access
- **Admin** 👤 – Institution management
- **Teacher** 📚 – Teaching and assessment
- **Student** 🎓 – Learning and assessments
- **Technician** 🔧 – System maintenance

### 6. Audit Logs (`superadmin-audit-logs`)
- View all system activity
- Filter by user, action, date
- Export logs (CSV/JSON)
- Clear old logs
- Track user actions (create, update, delete)
- Monitor login/logout events

### 7. Platform Settings (`superadmin-settings`)
**General Settings:**
- Platform name
- Support email
- Max users per institution

**Security Settings:**
- Session timeout configuration
- Two-factor authentication toggle
- Force password change on first login

### 8. Data Backups (`superadmin-backups`)
- Create manual backups
- View backup history
- Download backups
- Restore from backups
- Delete old backups
- Automatic backup scheduling (future)

## Navigation

### From Admin Portal
If you're logged in as a superadmin in the regular admin portal (`admin.html`), you'll see a **"Super Admin Portal"** button in the top-right corner of the dashboard.

### Direct Access
1. Open `superadmin.html` in your browser
2. Login with superadmin credentials
3. You'll be automatically redirected to the superadmin dashboard

## Security

### Access Control
- Only users with `superadmin` or `platform_admin` role can access
- Automatic redirect if unauthorized user attempts access
- Session-based authentication with JWT tokens

### Best Practices
1. **Change Default Password:** Immediately change the default superadmin password
2. **Limited Access:** Only grant superadmin access to trusted personnel
3. **Audit Regularly:** Review audit logs frequently
4. **Regular Backups:** Create backups before major changes
5. **2FA:** Enable two-factor authentication in production

## API Integration

The superadmin portal integrates with the same API endpoints as the admin portal, with additional superadmin-specific endpoints:

```javascript
// Superadmin-only API endpoints
GET    /api/superadmin/users          // List all users
POST   /api/superadmin/users          // Create user
DELETE /api/superadmin/users/:id      // Delete user
GET    /api/superadmin/institutions   // List institutions
POST   /api/superadmin/institutions   // Create institution
GET    /api/superadmin/system/health  // System health
GET    /api/superadmin/audit-logs     // Audit logs
GET    /api/superadmin/rbac/roles     // RBAC roles
POST   /api/superadmin/rbac/roles     // Create role
GET    /api/superadmin/backups        // List backups
POST   /api/superadmin/backups        // Create backup
```

## Local Storage Keys

The superadmin portal uses these localStorage keys:

| Key | Purpose |
|-----|---------|
| `yems_institutions` | Institution data |
| `yems_system_health` | System health metrics |
| `yems_rbac_roles` | RBAC role definitions |
| `yems_audit_logs` | Activity logs |
| `yems_platform_settings` | Global settings |
| `yems_backups` | Backup metadata |
| `yep_users` | All platform users |

## Customization

### Add New Features
1. Create a new route in `js/superadmin.js`
2. Register the route in the `boot()` function
3. Create the render function
4. Add navigation link in the sidebar

### Theme Colors
Edit CSS variables in `css/style.css`:
```css
:root {
  --maroon: #7B1D3C;      /* Primary brand color */
  --maroon-dark: #5E1530;
  --maroon-light: #9B2D54;
}
```

## Troubleshooting

### Can't Access Super Admin Portal
1. Ensure you're logged in as superadmin
2. Check browser console for errors
3. Verify role in localStorage: `JSON.parse(sessionStorage.getItem('yep_session')).role`
4. Clear cache and re-login

### Data Not Loading
1. Check if data is initialized: Open browser console and run `DataInit.init()`
2. Verify localStorage has data
3. Check API connectivity if using remote auth

### System Health Shows Issues
1. Check API server status
2. Verify database connection
3. Review audit logs for errors
4. Check server resources (storage, memory)

## Future Enhancements

- [ ] Real-time notifications
- [ ] Advanced analytics dashboard
- [ ] Bulk user operations
- [ ] Email notifications for critical events
- [ ] Automated backup scheduling
- [ ] System alerts and monitoring
- [ ] Multi-tenancy support
- [ ] Advanced RBAC with custom permissions
- [ ] API rate limiting
- [ ] Login history and session management

## Support

For technical support or questions:
- Check browser console for errors
- Review audit logs for activity details
- Consult the main README.md for general YEMS documentation

---

**Version:** 1.0.0  
**Last Updated:** March 2026
