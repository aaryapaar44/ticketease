# TicketEase

Flask + MySQL backend with the React (Figma) frontend.

```
ticketease/
├── app.py            Flask API; also serves the built site from static/
├── requirements.txt
├── finalp.sql        MySQL schema + sample data
├── static/           the BUILT React site (what Flask serves)
└── frontend/         React source (only needed if you want to edit the design)
```

## Run it (no Node needed)

1. Load the database (this DROPS and recreates `ticket_booking`):
   `mysql -u root -p < finalp.sql`
2. `pip install -r requirements.txt`
3. Set your MySQL password and start Flask:
   - Windows (PowerShell): `$env:DB_PASSWORD="your_password"; python app.py`
   - Mac/Linux: `DB_PASSWORD=your_password python app.py`
4. Open http://localhost:5001

## Edit the frontend (needs Node 22)

```
cd frontend
npm install
npm run dev      # http://localhost:5173, API calls go to Flask on 5001
npm run build    # writes the finished site into ../static
```
Keep `python app.py` running while using `npm run dev`.

## Cancelling a booking
My Bookings > **Cancel booking** (with a confirmation step). It calls
`POST /api/bookings/<id>/cancel`, which runs the `sp_cancel_booking` stored procedure:
tickets are cancelled (seats become available again), the total goes to 0 and a
successful payment is marked Refunded. Rules: only the owner can cancel, the whole
booking is cancelled at once, and it is blocked once the show has started.

## Notes
- Seed show dates are relative to the day you load `finalp.sql` (7 to 22 days ahead), so reload it if they ever run out.
- If the site shows "Something went wrong", open http://localhost:5001/api/health to check the DB connection (usually a wrong `DB_PASSWORD`).
- Seeded users in finalp.sql have placeholder passwords, so they cannot log in. Create an account with Sign up.
- Seat prices live in `SEAT_PRICES` at the top of app.py (the schema has no price column).
- Set a `SECRET_KEY` environment variable for anything beyond local use.
