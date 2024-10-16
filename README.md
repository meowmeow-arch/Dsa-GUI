
//just playing around


import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;

class Contact {
    String name;
    String phoneNumber;
    String email;

    public Contact(String name, String phoneNumber, String email) {
        this.name = name;
        this.phoneNumber = phoneNumber;
        this.email = email;
    }

    public String getName() {
        return name;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public String getEmail() {
        return email;
    }

    @Override
    public String toString() {
        return name + " | " + phoneNumber + " | " + (email != null ? email : "No email");
    }
}

public class Main extends JFrame implements ActionListener {
    private ArrayList<Contact> contacts = new ArrayList<>();
    private JTextField nameField, phoneField, emailField, searchField;
    private JTextArea resultArea;

    public Main() {
        // Frame settings
        setTitle("Phonebook Application");
        setSize(600, 600);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null); // Center the frame

        // Main Panel with GridBagLayout
        JPanel mainPanel = new JPanel();
        mainPanel.setLayout(new GridBagLayout());
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(10, 10, 10, 10); // Add padding around components
        gbc.anchor = GridBagConstraints.WEST;

        // Labels and Text Fields for inserting contacts
        gbc.gridx = 0;
        gbc.gridy = 0;
        mainPanel.add(new JLabel("Name:"), gbc);

        nameField = new JTextField(20);
        gbc.gridx = 1;
        mainPanel.add(nameField, gbc);

        gbc.gridx = 0;
        gbc.gridy = 1;
        mainPanel.add(new JLabel("Phone Number:"), gbc);

        phoneField = new JTextField(20);
        gbc.gridx = 1;
        mainPanel.add(phoneField, gbc);

        gbc.gridx = 0;
        gbc.gridy = 2;
        mainPanel.add(new JLabel("Email (optional):"), gbc);

        emailField = new JTextField(20);
        gbc.gridx = 1;
        mainPanel.add(emailField, gbc);

        // Insert button
        JButton insertButton = new JButton("Insert Contact");
        insertButton.addActionListener(this);
        gbc.gridx = 0;
        gbc.gridy = 3;
        gbc.gridwidth = 2;
        mainPanel.add(insertButton, gbc);

        // Search section
        gbc.gridx = 0;
        gbc.gridy = 4;
        mainPanel.add(new JLabel("Search Contact by Name or Phone:"), gbc);

        searchField = new JTextField(20);
        gbc.gridx = 1;
        mainPanel.add(searchField, gbc);

        JButton searchButton = new JButton("Search");
        searchButton.addActionListener(this);
        gbc.gridx = 0;
        gbc.gridy = 5;
        gbc.gridwidth = 2;
        mainPanel.add(searchButton, gbc);

        // Display all contacts
        JButton displayButton = new JButton("Display All Contacts");
        displayButton.addActionListener(this);
        gbc.gridx = 0;
        gbc.gridy = 6;
        gbc.gridwidth = 2;
        mainPanel.add(displayButton, gbc);

        // Delete button
        JButton deleteButton = new JButton("Delete Contact");
        deleteButton.addActionListener(this);
        gbc.gridx = 0;
        gbc.gridy = 7;
        gbc.gridwidth = 2;
        mainPanel.add(deleteButton, gbc);

        // Update button
        JButton updateButton = new JButton("Update Contact");
        updateButton.addActionListener(this);
        gbc.gridx = 0;
        gbc.gridy = 8;
        gbc.gridwidth = 2;
        mainPanel.add(updateButton, gbc);

        // Analyze efficiency button
        JButton analyzeButton = new JButton("Analyze Search Efficiency");
        analyzeButton.addActionListener(this);
        gbc.gridx = 0;
        gbc.gridy = 9;
        gbc.gridwidth = 2;
        mainPanel.add(analyzeButton, gbc);

        // Result area for displaying contacts
        resultArea = new JTextArea(20, 60);
        resultArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(resultArea);
        scrollPane.setPreferredSize(new Dimension(600, 300));
        gbc.gridx = 0;
        gbc.gridy = 100;
        gbc.gridwidth = 200;
        mainPanel.add(scrollPane, gbc);

        // Add the main panel to the frame
        add(mainPanel, BorderLayout.CENTER);

        setVisible(true);
    }

    // Function to insert a new contact
    public void insertContact(String name, String phoneNumber, String email) {
        Contact newContact = new Contact(name, phoneNumber, email);
        contacts.add(newContact);
        sortContacts();
        JOptionPane.showMessageDialog(this, "Contact added successfully!");
    }

    // Function to sort contacts by name
    public void sortContacts() {
        Collections.sort(contacts, Comparator.comparing(Contact::getName));
    }

    // Binary search to find contact by name, with fallback linear search for phone
    public Contact searchContact(String query) {
        int left = 0;
        int right = contacts.size() - 1;
        query = query.toLowerCase();

        while (left <= right) {
            int mid = (left + right) / 2;
            String contactName = contacts.get(mid).getName().toLowerCase();

            if (contactName.equals(query)) {
                return contacts.get(mid);
            } else if (contactName.compareTo(query) < 0) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        // If name is not found, search by phone number
        for (Contact contact : contacts) {
            if (contact.getPhoneNumber().equals(query)) {
                return contact;
            }
        }
        return null;
    }

    // Function to display all contacts
    public void displayAllContacts() {
        if (contacts.isEmpty()) {
            resultArea.setText("No contacts to display.");
        } else {
            StringBuilder result = new StringBuilder("All Contacts:\n");
            for (Contact contact : contacts) {
                result.append(contact.toString()).append("\n");
            }
            resultArea.setText(result.toString());
        }
    }

    // Function to delete a contact
    public boolean deleteContact(String query) {
        for (int i = 0; i < contacts.size(); i++) {
            if (contacts.get(i).getName().equalsIgnoreCase(query) ||
                    contacts.get(i).getPhoneNumber().equals(query)) {
                contacts.remove(i);
                return true;
            }
        }
        return false;
    }

    // Function to update a contact
    public boolean updateContact(String query, String newName, String newPhoneNumber, String newEmail) {
        Contact contact = searchContact(query);
        if (contact != null) {
            contact.name = newName;
            contact.phoneNumber = newPhoneNumber;
            contact.email = newEmail;
            sortContacts();
            return true;
        }
        return false;
    }

    // Analyze search efficiency
    public void analyzeSearchEfficiency() {
        String analysis = """
            Search Efficiency Analysis:
            1. Best Case (Binary Search): O(1) - When the contact is at the middle of the sorted array
            2. Average Case (Binary Search): O(log n) - Where n is the number of contacts
            3. Worst Case (Binary Search + Linear Search): O(log n + n) - When searching by phone number
            """;
        resultArea.setText(analysis);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        String command = e.getActionCommand();

        switch (command) {
            case "Insert Contact":
                String name = nameField.getText();
                String phoneNumber = phoneField.getText();
                String email = emailField.getText().isEmpty() ? null : emailField.getText();
                if (!name.isEmpty() && !phoneNumber.isEmpty()) {
                    insertContact(name, phoneNumber, email);
                    nameField.setText("");
                    phoneField.setText("");
                    emailField.setText("");
                } else {
                    JOptionPane.showMessageDialog(this, "Name and Phone Number are required.");
                }
                break;

            case "Search":
                String query = searchField.getText();
                if (!query.isEmpty()) {
                    Contact found = searchContact(query);
                    if (found != null) {
                        resultArea.setText("Found Contact:\n" + found.toString());
                    } else {
                        resultArea.setText("Contact not found.");
                    }
                } else {
                    JOptionPane.showMessageDialog(this, "Enter a name or phone number to search.");
                }
                break;

            case "Display All Contacts":
                displayAllContacts();
                break;

            case "Delete Contact":
                String deleteQuery = searchField.getText();
                if (deleteContact(deleteQuery)) {
                    resultArea.setText("Contact deleted successfully.");
                } else {
                    resultArea.setText("Contact not found.");
                }
                break;

            case "Update Contact":
                String updateQuery = searchField.getText();
                String newName = JOptionPane.showInputDialog("Enter new name:");
                String newPhone = JOptionPane.showInputDialog("Enter new phone number:");
                String newEmail = JOptionPane.showInputDialog("Enter new email (optional):");
                if (updateContact(updateQuery, newName, newPhone, newEmail)) {
                    resultArea.setText("Contact updated successfully.");
                } else {
                    resultArea.setText("Contact not found.");
                }
                break;

            case "Analyze Search Efficiency":
                analyzeSearchEfficiency();
                break;
        }
    }

    public static void main(String[] args) {
        new Main();
    }
}
