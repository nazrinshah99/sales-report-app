# sales-report-app
import React, { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";

const initialUsers = [];

export default function SalesReportApp() {
  const [users, setUsers] = useState(initialUsers);
  const [newUser, setNewUser] = useState({ name: "", channel: "" });

  const handleAddUser = () => {
    setUsers([...users, { ...newUser, branch: "JKT", days: Array(30).fill("") }]);
    setNewUser({ name: "", channel: "" });
  };

  const handleDayChange = (userIndex, dayIndex, value) => {
    const updatedUsers = [...users];
    updatedUsers[userIndex].days[dayIndex] = value;
    setUsers(updatedUsers);
  };

  const handleFileUpload = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    reader.onload = (event) => {
      const text = event.target.result;
      const rows = text.split("\n").map(row => row.split(","));
      const importedUsers = rows.slice(1).filter(row => row.length >= 33).map(row => ({
        name: row[0],
        channel: row[2],
        branch: "JKT",
        days: row.slice(3, 33),
      }));
      setUsers(importedUsers);
    };
    reader.readAsText(file);
  };

  return (
    <div className="p-6 space-y-4">
      <Card>
        <CardContent className="grid grid-cols-6 gap-2 p-4">
          {Object.keys(newUser).map((key) => (
            <Input
              key={key}
              placeholder={key.toUpperCase()}
              value={newUser[key]}
              onChange={(e) => setNewUser({ ...newUser, [key]: e.target.value })}
            />
          ))}
          <Button onClick={handleAddUser}>Add User</Button>
          <Input type="file" accept=".csv" onChange={handleFileUpload} />
        </CardContent>
      </Card>

      <Card>
        <CardContent className="overflow-auto">
          <Table>
            <TableHeader>
              <TableRow>
                <TableHead>Name</TableHead>
                <TableHead>Branch</TableHead>
                <TableHead>Channel</TableHead>
                {[...Array(30)].map((_, i) => (
                  <TableHead key={i}>{i + 1}</TableHead>
                ))}
              </TableRow>
            </TableHeader>
            <TableBody>
              {users.map((user, userIndex) => (
                <TableRow key={userIndex}>
                  <TableCell>{user.name}</TableCell>
                  <TableCell>{user.branch}</TableCell>
                  <TableCell>{user.channel}</TableCell>
                  {user.days.map((day, dayIndex) => (
                    <TableCell key={dayIndex}>
                      <Input
                        value={day}
                        onChange={(e) => handleDayChange(userIndex, dayIndex, e.target.value)}
                        className="w-20"
                      />
                    </TableCell>
                  ))}
                </TableRow>
              ))}
            </TableBody>
          </Table>
        </CardContent>
      </Card>
    </div>
  );
}
