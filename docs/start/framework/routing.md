import { useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { Input } from "@/components/ui/input"; import { Textarea } from "@/components/ui/textarea"; import { Label } from "@/components/ui/label"; import { Upload, History, Search } from "lucide-react";

export default function MaintenanceForm() { const [form, setForm] = useState({ date: new Date().toISOString().split("T")[0], equipment: "", shift: "", lubrication: false, inspection: false, temperature: false, remarks: "", technician: "", photo: null, });

const [logs, setLogs] = useState([]); const [viewLogs, setViewLogs] = useState(false); const [search, setSearch] = useState(""); const [filterShift, setFilterShift] = useState(""); const [filterEquipment, setFilterEquipment] = useState("");

const handleChange = (e) => { const { name, type, checked, value, files } = e.target; setForm({ ...form, [name]: type === "checkbox" ? checked : type === "file" ? files[0] : value, }); };

const handleSubmit = () => { const newLog = { ...form, photo: form.photo ? form.photo.name : "No photo uploaded", }; setLogs([...logs, newLog]); alert("Maintenance log submitted successfully!"); };

const handleExport = () => { alert("Export to Excel feature is coming soon."); };

const filteredLogs = logs.filter((log) => { const searchMatch = log.technician.toLowerCase().includes(search.toLowerCase()) || log.equipment.toLowerCase().includes(search.toLowerCase()) || log.remarks.toLowerCase().includes(search.toLowerCase()); const shiftMatch = filterShift ? log.shift === filterShift : true; const equipmentMatch = filterEquipment ? log.equipment === filterEquipment : true; return searchMatch && shiftMatch && equipmentMatch; });

return ( <div className="max-w-xl mx-auto p-4 space-y-4"> <Card> <CardContent className="space-y-4 p-4"> <h2 className="text-xl font-semibold">Daily Maintenance Log</h2>

<div>
        <Label>Date</Label>
        <Input type="date" name="date" value={form.date} onChange={handleChange} />
      </div>

      <div>
        <Label>Equipment</Label>
        <Input name="equipment" placeholder="e.g., Cane Crusher 1" onChange={handleChange} />
      </div>

      <div>
        <Label>Shift</Label>
        <Input name="shift" placeholder="A / B / C" onChange={handleChange} />
      </div>

      <div className="space-y-2">
        <Label>Checklist</Label>
        <div className="space-x-2">
          <label>
            <input type="checkbox" name="lubrication" onChange={handleChange} /> Lubrication
          </label>
          <label>
            <input type="checkbox" name="inspection" onChange={handleChange} /> Visual Inspection
          </label>
          <label>
            <input type="checkbox" name="temperature" onChange={handleChange} /> Temperature Check
          </label>
        </div>
      </div>

      <div>
        <Label>Remarks</Label>
        <Textarea name="remarks" onChange={handleChange} placeholder="Any issues or observations..." />
      </div>

      <div>
        <Label>Technician Name</Label>
        <Input name="technician" onChange={handleChange} placeholder="e.g., John D." />
      </div>

      <div>
        <Label>Upload Photo</Label>
        <Input type="file" name="photo" onChange={handleChange} accept="image/*" />
      </div>

      <Button className="w-full" onClick={handleSubmit}>Submit</Button>
      <Button variant="outline" className="w-full mt-2" onClick={handleExport}>
        <Upload className="mr-2 h-4 w-4" /> Export Logs (Excel)
      </Button>
      <Button variant="secondary" className="w-full mt-2" onClick={() => setViewLogs(!viewLogs)}>
        <History className="mr-2 h-4 w-4" /> {viewLogs ? "Hide Logs" : "View Logs"}
      </Button>
    </CardContent>
  </Card>

  {viewLogs && (
    <Card>
      <CardContent className="p-4 space-y-4">
        <h3 className="text-lg font-semibold">Maintenance History</h3>

        <div className="flex gap-2">
          <Input
            placeholder="Search by technician, equipment, remarks"
            value={search}
            onChange={(e) => setSearch(e.target.value)}
          />
          <Input
            placeholder="Filter by Equipment"
            value={filterEquipment}
            onChange={(e) => setFilterEquipment(e.target.value)}
          />
          <Input
            placeholder="Filter by Shift (A/B/C)"
            value={filterShift}
            onChange={(e) => setFilterShift(e.target.value)}
          />
        </div>

        {filteredLogs.length === 0 ? (
          <p className="text-sm text-muted">No matching logs found.</p>
        ) : (
          <ul className="space-y-2">
            {filteredLogs.map((log, idx) => (
              <li key={idx} className="border rounded p-2">
                <p><strong>Date:</strong> {log.date}</p>
                <p><strong>Equipment:</strong> {log.equipment}</p>
                <p><strong>Shift:</strong> {log.shift}</p>
                <p><strong>Technician:</strong> {log.technician}</p>
                <p><strong>Checklist:</strong> {`
                  ${log.lubrication ? "Lubrication, " : ""}
                  ${log.inspection ? "Visual Inspection, " : ""}
                  ${log.temperature ? "Temperature Check" : ""}`}</p>
                <p><strong>Remarks:</strong> {log.remarks}</p>
                <p><strong>Photo:</strong> {log.photo}</p>
              </li>
            ))}
          </ul>
        )}
      </CardContent>
    </Card>
  )}
</div>

); }

