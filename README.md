import React, { useState, useMemo } from 'react';

// --- 1. Predefined Events Array (The Data Source) ---
const predefinedEvents = [
  // NOTE: Dates are formatted as 'YYYY-MM-DD'
  { date: '2025-10-17', title: '🎉 Code Review', description: 'Review final pull requests for the deployment.' },
  { date: '2025-10-25', title: '🤝 Team Meeting', description: 'Discuss Q4 objectives and budget.' },
  { date: '2025-10-05', title: '🏖️ Holiday', description: 'Local Day Off.' },
  { date: '2025-10-09', title: '💻 Client Demo', description: 'Present the new features to the client.' },
];

// --- 2. Helper Function ---
// Formats a Date object to the 'YYYY-MM-DD' string format for comparison
const formatDate = (date) => {
  const y = date.getFullYear();
  // +1 because getMonth() is 0-indexed (0=Jan)
  const m = String(date.getMonth() + 1).padStart(2, '0'); 
  const d = String(date.getDate()).padStart(2, '0');
  return `${y}-${m}-${d}`;
};

// --- 3. Interactive Calendar Component ---
const InteractiveCalendar = () => {
  // Initialize state with today's date, which is Oct 17, 2025, according to the system time.
  const [selectedDateStr, setSelectedDateStr] = useState(formatDate(new Date())); 

  // Determine the current month/year context
  const today = new Date();
  const currentMonth = today.getMonth();
  const currentYear = today.getFullYear();
  
  const firstDayOfMonth = new Date(currentYear, currentMonth, 1).getDay(); // 0 (Sun) - 6 (Sat)
  // Get the last day of the current month
  const daysInMonth = new Date(currentYear, currentMonth + 1, 0).getDate(); 

  // Memoize event filtering for performance
  const eventsForSelectedDate = useMemo(() => {
    if (!selectedDateStr) return [];
    // Filters the predefined array for events matching the selected date
    return predefinedEvents.filter(event => event.date === selectedDateStr);
  }, [selectedDateStr]);

  // Event handler to update state when a date is clicked
  const handleDateClick = (day) => {
    const newDate = new Date(currentYear, currentMonth, day);
    const newDateStr = formatDate(newDate);
    setSelectedDateStr(newDateStr);
  };

  // Build the array of calendar cell elements
  const calendarDays = [];

  // Add empty filler cells for days before the 1st
  for (let i = 0; i < firstDayOfMonth; i++) {
    calendarDays.push(<div key={`empty-${i}`} style={styles.calendarDayEmpty}></div>);
  }

  // Add the actual days of the month
  for (let day = 1; day <= daysInMonth; day++) {
    const dayDate = new Date(currentYear, currentMonth, day);
    const dayDateStr = formatDate(dayDate);
    const isSelected = dayDateStr === selectedDateStr;

    calendarDays.push(
      <div 
        key={day} 
        // Apply base style and conditionally merge the 'selected' style
        style={{...styles.calendarDay, ...(isSelected ? styles.calendarDaySelected : {})}}
        onClick={() => handleDateClick(day)}
      >
        {day}
      </div>
    );
  }

  return (
    <div style={styles.container}>
      <h2>
        🗓️ {today.toLocaleDateString('en-US', { month: 'long', year: 'numeric' })}
      </h2>
      
      {/* Calendar Grid */}
      <div style={styles.calendarGrid}>
        {['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'].map(day => (
          <div key={day} style={styles.calendarDayHeader}>{day}}>{day}</div>
        ))}
        {calendarDays}
      </div>

      {/* Event Display Details */}
      <div style={styles.eventDisplayArea}>
        <h3>Event Details for: **{selectedDateStr || '---'}**</h3>
        
        {selectedDateStr && eventsForSelectedDate.length > 0 ? (
          eventsForSelectedDate.map((event, index) => (
            <div key={index} style={styles.eventDetail}>
              <p style={styles.eventTitle}>{event.title}</p>
              <p style={styles.eventDescription}>{event.description}</p>
            </div>
          ))
        ) : selectedDateStr ? (
          <p style={{color: '#888'}}>No events scheduled for this date.</p>
        ) : (
          <p>Click a date on the calendar to see its events.</p>
        )}
      </div>
    </div>
  );
};

// --- 4. Inline CSS Styles (Styles Object) ---
const styles = {
  container: {
    maxWidth: '700px',
    margin: '40px auto',
    padding: '25px',
    border: '1px solid #ddd',
    borderRadius: '12px',
    fontFamily: 'Roboto, Arial, sans-serif',
    boxShadow: '0 6px 15px rgba(0,0,0,0.1)',
    backgroundColor: '#fff',
  },
  calendarGrid: {
    display: 'grid',
    gridTemplateColumns: 'repeat(7, 1fr)',
    gap: '6px',
    marginBottom: '25px',
  },
  calendarDayHeader: {
    textAlign: 'center',
    fontWeight: 'bold',
    padding: '12px 0',
    backgroundColor: '#e9ecef',
    borderRadius: '6px',
    fontSize: '0.9em',
    color: '#333',
  },
  calendarDay: {
    textAlign: 'center',
    padding: '18px 0',
    border: '1px solid #f0f0f0',
    cursor: 'pointer',
    transition: 'background-color 0.2s, transform 0.1s',
    borderRadius: '6px',
    fontWeight: '500',
  },
  calendarDaySelected: {
    backgroundColor: '#17a2b8', /* Teal/Sky Blue for highlight */
    color: 'white',
    fontWeight: 'bold',
    border: '1px solid #138496',
    boxShadow: '0 2px 5px rgba(0, 123, 255, 0.4)',
    transform: 'scale(1.05)',
  },
  calendarDayEmpty: {
    padding: '18px 0',
    backgroundColor: '#fafafa',
    cursor: 'default',
    border: '1px solid transparent',
  },
  eventDisplayArea: {
    marginTop: '30px',
    padding: '20px',
    borderTop: '3px solid #17a2b8',
    backgroundColor: '#f7fbfc',
    borderRadius: '8px',
  },
  eventDetail: {
    borderLeft: '5px solid #ffc107',
    paddingLeft: '15px',
    margin: '15px 0',
    backgroundColor: '#fffef4',
    borderRadius: '4px',
    padding: '10px 15px 10px 15px',
  },
  eventTitle: {
      fontWeight: 'bold',
      margin: '5px 0 3px 0',
      color: '#333',
  },
  eventDescription: {
      fontSize: '0.95em',
      color: '#555',
      margin: '0 0 5px 0',
  }
};

export default InteractiveCalendar;
