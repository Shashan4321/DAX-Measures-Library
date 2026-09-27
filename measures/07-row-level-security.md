# 07 · Row-level security (RLS)

### Static role: India only
Role filter on `dim_store`:
```dax
dim_store[country] = "India"
```
**Example output:** Total Revenue 2025 for a user in this role → **₹1,308,424,605** (instead of ₹1,584,825,489)

### Dynamic role: each manager sees their own countries
Mapping table `security_user_country (user_email, country)`, hidden. Role filter on `dim_store`:
```dax
dim_store[country]
    IN CALCULATETABLE (
        VALUES ( security_user_country[country] ),
        security_user_country[user_email] = USERPRINCIPALNAME ()
    )
```
One person can map to several countries (e.g. an APAC head → India + Singapore) without changing the model.

### Hierarchy RLS (manager sees their team), HR example
```dax
PATHCONTAINS (
    dim_employee[manager_path],
    LOOKUPVALUE ( dim_employee[employee_id], dim_employee[email], USERPRINCIPALNAME () )
)
```
where `manager_path = PATH ( dim_employee[employee_id], dim_employee[manager_id] )`.

**Test it:** Modeling → View as → Other user → type an e-mail from the mapping table.
