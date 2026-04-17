# Security Test Plan: Drug Formulation Protection

## Objective
Verify that encrypted drug formulations are protected against unauthorized access, that password storage is secure, and that audit events record key actions.

## Scope
- AES-256 encryption of drug formulation payloads.
- Access controls for formulation visibility.
- Password hashing and authentication handling.
- Immutable audit trail recording.

## Test Cases

1. Encryption verification
   - Step: Create a new formulation as a Researcher or Production Manager.
   - Expected: The database stores binary `encrypted_payload` and `nonce`, not plaintext.
   - Expected: The formulation detail is only visible after decryption for authorized roles.

2. Decryption access control
   - Step: Log in as a Customer or Sales Staff and view formulations.
   - Expected: Formulation details are shown as `Restricted`.
   - Step: Log in as Researcher or Regulatory Affairs and view formulations.
   - Expected: Decrypted formulation content is displayed.

3. Password hashing
   - Step: Register a new user and inspect `password_hash` in the database.
   - Expected: Stored value is non-plaintext, and bcrypt format is used.
   - Step: Attempt login with correct and incorrect passwords.
   - Expected: Only the correct password authenticates.

4. Authorization enforcement
   - Step: Attempt to open `/formulations/new` with a Customer session.
   - Expected: Access denied and redirect to login.
   - Step: Attempt to open `/audit` with Warehouse Staff session.
   - Expected: Access denied due to role restrictions.

5. Audit trail integrity
   - Step: Create formulation and update inventory.
   - Expected: Audit entries are appended with `actor`, `role`, `action`, `target`, and `timestamp`.
   - Step: Attempt to modify an existing audit event through the UI.
   - Expected: No UI or endpoint exists to update or delete audit entries.

6. Data boundary checks
   - Step: Submit a formulation name longer than 120 characters.
   - Expected: Validation prevents saving or truncates safely.
   - Step: Submit negative inventory quantities.
   - Expected: The system rejects invalid inventory updates.

## Notes
- Use SQL inspection or an admin tool to verify encrypted payload storage.
- Verify environment `SECRET_KEY`/`ENCRYPTION_KEY` is not hard-coded in production.
- For a real deployment, rotate encryption keys and secure the key management system.
