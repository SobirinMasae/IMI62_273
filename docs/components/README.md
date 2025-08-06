# Component Documentation

This section contains comprehensive documentation for all UI components, layout components, and interactive elements.

## 📚 Table of Contents

- [UI Components](#ui-components)
- [Layout Components](#layout-components)
- [Form Components](#form-components)
- [Data Display Components](#data-display-components)
- [Interactive Components](#interactive-components)
- [Styling and Theming](#styling-and-theming)
- [Accessibility](#accessibility)
- [Performance](#performance)

## 🎨 UI Components

### Button

A versatile button component with multiple variants and states.

**Props:**
```typescript
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  loading?: boolean;
  icon?: React.ReactNode;
  iconPosition?: 'left' | 'right';
  fullWidth?: boolean;
  onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
  children: React.ReactNode;
  className?: string;
  type?: 'button' | 'submit' | 'reset';
}
```

**Examples:**
```jsx
import { Button } from 'your-component-library';

// Basic usage
<Button onClick={() => console.log('Clicked!')}>
  Click me
</Button>

// With variants
<Button variant="primary">Primary</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="danger">Danger</Button>

// With sizes
<Button size="sm">Small</Button>
<Button size="md">Medium</Button>
<Button size="lg">Large</Button>

// With icons
import { SaveIcon, ArrowRightIcon } from 'your-icons';

<Button icon={<SaveIcon />} iconPosition="left">
  Save
</Button>
<Button icon={<ArrowRightIcon />} iconPosition="right">
  Continue
</Button>

// Loading state
<Button loading>Loading...</Button>

// Disabled state
<Button disabled>Disabled</Button>

// Full width
<Button fullWidth>Full Width Button</Button>
```

**Styling:**
```css
/* Custom styling */
.custom-button {
  border-radius: 12px;
  font-weight: 600;
  text-transform: uppercase;
}
```

---

### Input

A flexible input component with validation and various input types.

**Props:**
```typescript
interface InputProps {
  type?: 'text' | 'email' | 'password' | 'number' | 'tel' | 'url' | 'search';
  value?: string;
  defaultValue?: string;
  placeholder?: string;
  disabled?: boolean;
  required?: boolean;
  readOnly?: boolean;
  error?: string;
  helperText?: string;
  label?: string;
  icon?: React.ReactNode;
  iconPosition?: 'left' | 'right';
  size?: 'sm' | 'md' | 'lg';
  fullWidth?: boolean;
  onChange?: (event: React.ChangeEvent<HTMLInputElement>) => void;
  onFocus?: (event: React.FocusEvent<HTMLInputElement>) => void;
  onBlur?: (event: React.FocusEvent<HTMLInputElement>) => void;
  className?: string;
}
```

**Examples:**
```jsx
import { Input } from 'your-component-library';
import { SearchIcon, EyeIcon } from 'your-icons';

// Basic usage
<Input 
  placeholder="Enter your name"
  onChange={(e) => setName(e.target.value)}
/>

// With label and helper text
<Input
  label="Email Address"
  type="email"
  placeholder="john@example.com"
  helperText="We'll never share your email"
  required
/>

// With error state
<Input
  label="Password"
  type="password"
  error="Password must be at least 8 characters"
  value={password}
  onChange={(e) => setPassword(e.target.value)}
/>

// With icons
<Input
  icon={<SearchIcon />}
  iconPosition="left"
  placeholder="Search..."
/>

// Different sizes
<Input size="sm" placeholder="Small input" />
<Input size="md" placeholder="Medium input" />
<Input size="lg" placeholder="Large input" />

// Number input with validation
<Input
  type="number"
  label="Age"
  min="0"
  max="120"
  onChange={(e) => {
    const value = parseInt(e.target.value);
    if (value >= 0 && value <= 120) {
      setAge(value);
    }
  }}
/>
```

---

### Modal

A flexible modal component with animation and focus management.

**Props:**
```typescript
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  size?: 'sm' | 'md' | 'lg' | 'xl' | 'full';
  closeOnOverlayClick?: boolean;
  closeOnEscape?: boolean;
  showCloseButton?: boolean;
  title?: string;
  children: React.ReactNode;
  className?: string;
  overlayClassName?: string;
}
```

**Examples:**
```jsx
import { Modal, Button } from 'your-component-library';

function App() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <Button onClick={() => setIsOpen(true)}>
        Open Modal
      </Button>

      <Modal
        isOpen={isOpen}
        onClose={() => setIsOpen(false)}
        title="Confirm Action"
        size="md"
      >
        <p>Are you sure you want to continue?</p>
        <div style={{ marginTop: '20px' }}>
          <Button 
            onClick={() => setIsOpen(false)}
            variant="outline"
            style={{ marginRight: '10px' }}
          >
            Cancel
          </Button>
          <Button 
            onClick={() => {
              // Perform action
              setIsOpen(false);
            }}
            variant="primary"
          >
            Confirm
          </Button>
        </div>
      </Modal>
    </>
  );
}

// Large modal with custom content
<Modal
  isOpen={showLargeModal}
  onClose={() => setShowLargeModal(false)}
  size="lg"
  title="User Profile"
>
  <form onSubmit={handleSubmit}>
    <Input label="Name" value={name} onChange={(e) => setName(e.target.value)} />
    <Input label="Email" type="email" value={email} onChange={(e) => setEmail(e.target.value)} />
    <Button type="submit" fullWidth>Save Changes</Button>
  </form>
</Modal>
```

---

### Card

A container component for organizing content with consistent styling.

**Props:**
```typescript
interface CardProps {
  variant?: 'default' | 'outlined' | 'elevated';
  padding?: 'none' | 'sm' | 'md' | 'lg';
  hoverable?: boolean;
  children: React.ReactNode;
  className?: string;
  onClick?: () => void;
}
```

**Examples:**
```jsx
import { Card, Button } from 'your-component-library';

// Basic card
<Card>
  <h3>Card Title</h3>
  <p>This is some card content.</p>
</Card>

// Outlined card
<Card variant="outlined" padding="lg">
  <h3>Outlined Card</h3>
  <p>Card with border styling.</p>
</Card>

// Elevated card with hover effect
<Card variant="elevated" hoverable>
  <h3>Product Card</h3>
  <p>$99.99</p>
  <Button>Add to Cart</Button>
</Card>

// Clickable card
<Card 
  hoverable 
  onClick={() => navigate('/product/123')}
  style={{ cursor: 'pointer' }}
>
  <h3>Clickable Card</h3>
  <p>Click anywhere on this card</p>
</Card>
```

## 📐 Layout Components

### Container

A responsive container component that centers content with max-width constraints.

**Props:**
```typescript
interface ContainerProps {
  size?: 'sm' | 'md' | 'lg' | 'xl' | 'full';
  padding?: boolean;
  children: React.ReactNode;
  className?: string;
}
```

**Examples:**
```jsx
import { Container } from 'your-component-library';

// Basic container
<Container>
  <h1>Page Content</h1>
  <p>This content is centered and has a max width.</p>
</Container>

// Different sizes
<Container size="sm">Small container</Container>
<Container size="lg">Large container</Container>
<Container size="full">Full width container</Container>

// Without padding
<Container padding={false}>
  Content without built-in padding
</Container>
```

---

### Grid

A flexible CSS Grid component for layout.

**Props:**
```typescript
interface GridProps {
  columns?: number | string;
  rows?: number | string;
  gap?: string | number;
  columnGap?: string | number;
  rowGap?: string | number;
  autoFlow?: 'row' | 'column' | 'row dense' | 'column dense';
  children: React.ReactNode;
  className?: string;
}

interface GridItemProps {
  column?: string | number;
  row?: string | number;
  columnSpan?: number;
  rowSpan?: number;
  children: React.ReactNode;
  className?: string;
}
```

**Examples:**
```jsx
import { Grid, GridItem } from 'your-component-library';

// Basic grid
<Grid columns={3} gap="20px">
  <GridItem>Item 1</GridItem>
  <GridItem>Item 2</GridItem>
  <GridItem>Item 3</GridItem>
  <GridItem>Item 4</GridItem>
  <GridItem>Item 5</GridItem>
  <GridItem>Item 6</GridItem>
</Grid>

// Responsive grid
<Grid 
  columns="repeat(auto-fit, minmax(250px, 1fr))" 
  gap="16px"
>
  <Card>Card 1</Card>
  <Card>Card 2</Card>
  <Card>Card 3</Card>
</Grid>

// Grid with spanning items
<Grid columns={4} rows={3} gap="10px">
  <GridItem columnSpan={2} rowSpan={2}>
    Large item
  </GridItem>
  <GridItem>Item 2</GridItem>
  <GridItem>Item 3</GridItem>
  <GridItem>Item 4</GridItem>
  <GridItem>Item 5</GridItem>
</Grid>
```

---

### Flex

A flexbox container component with common flex properties.

**Props:**
```typescript
interface FlexProps {
  direction?: 'row' | 'column' | 'row-reverse' | 'column-reverse';
  wrap?: 'nowrap' | 'wrap' | 'wrap-reverse';
  justify?: 'flex-start' | 'flex-end' | 'center' | 'space-between' | 'space-around' | 'space-evenly';
  align?: 'flex-start' | 'flex-end' | 'center' | 'stretch' | 'baseline';
  gap?: string | number;
  children: React.ReactNode;
  className?: string;
}
```

**Examples:**
```jsx
import { Flex, Button } from 'your-component-library';

// Horizontal layout
<Flex justify="space-between" align="center">
  <h1>Page Title</h1>
  <Button>Action</Button>
</Flex>

// Vertical layout
<Flex direction="column" gap="16px">
  <Input placeholder="Field 1" />
  <Input placeholder="Field 2" />
  <Button>Submit</Button>
</Flex>

// Centered content
<Flex justify="center" align="center" style={{ height: '200px' }}>
  <Card>Centered card</Card>
</Flex>

// Button group
<Flex gap="8px">
  <Button variant="outline">Cancel</Button>
  <Button variant="primary">Save</Button>
  <Button variant="danger">Delete</Button>
</Flex>
```

## 📝 Form Components

### Form

A form wrapper component with validation and submission handling.

**Props:**
```typescript
interface FormProps {
  onSubmit: (data: any) => void | Promise<void>;
  validation?: ValidationSchema;
  initialValues?: any;
  children: React.ReactNode;
  className?: string;
}
```

**Examples:**
```jsx
import { Form, Input, Button, Select } from 'your-component-library';

// Basic form
<Form onSubmit={(data) => console.log('Form data:', data)}>
  <Input name="name" label="Full Name" required />
  <Input name="email" label="Email" type="email" required />
  <Button type="submit">Submit</Button>
</Form>

// Form with validation
const validationSchema = {
  name: { required: true, minLength: 2 },
  email: { required: true, type: 'email' },
  age: { required: true, type: 'number', min: 18 }
};

<Form 
  onSubmit={handleSubmit}
  validation={validationSchema}
  initialValues={{ name: '', email: '', age: '' }}
>
  <Input name="name" label="Full Name" />
  <Input name="email" label="Email" type="email" />
  <Input name="age" label="Age" type="number" />
  <Button type="submit">Create Account</Button>
</Form>
```

---

### Select

A dropdown select component with search and multi-select capabilities.

**Props:**
```typescript
interface SelectProps {
  options: Array<{ value: string; label: string; disabled?: boolean }>;
  value?: string | string[];
  defaultValue?: string | string[];
  placeholder?: string;
  searchable?: boolean;
  multiple?: boolean;
  disabled?: boolean;
  clearable?: boolean;
  label?: string;
  error?: string;
  onChange?: (value: string | string[]) => void;
  className?: string;
}
```

**Examples:**
```jsx
import { Select } from 'your-component-library';

const countries = [
  { value: 'us', label: 'United States' },
  { value: 'ca', label: 'Canada' },
  { value: 'uk', label: 'United Kingdom' },
  { value: 'au', label: 'Australia' }
];

// Basic select
<Select
  label="Country"
  options={countries}
  placeholder="Select a country"
  onChange={(value) => setCountry(value)}
/>

// Searchable select
<Select
  label="Country"
  options={countries}
  searchable
  placeholder="Search for a country"
  onChange={(value) => setCountry(value)}
/>

// Multi-select
<Select
  label="Languages"
  options={[
    { value: 'js', label: 'JavaScript' },
    { value: 'ts', label: 'TypeScript' },
    { value: 'py', label: 'Python' },
    { value: 'go', label: 'Go' }
  ]}
  multiple
  placeholder="Select programming languages"
  onChange={(values) => setLanguages(values)}
/>
```

---

### Checkbox

A checkbox component with indeterminate state support.

**Props:**
```typescript
interface CheckboxProps {
  checked?: boolean;
  defaultChecked?: boolean;
  indeterminate?: boolean;
  disabled?: boolean;
  label?: string;
  description?: string;
  error?: string;
  onChange?: (checked: boolean) => void;
  className?: string;
}
```

**Examples:**
```jsx
import { Checkbox } from 'your-component-library';

// Basic checkbox
<Checkbox
  label="Accept terms and conditions"
  onChange={(checked) => setAccepted(checked)}
/>

// Checkbox with description
<Checkbox
  label="Email notifications"
  description="Receive updates about your account and new features"
  checked={emailNotifications}
  onChange={setEmailNotifications}
/>

// Indeterminate checkbox (for "select all" scenarios)
<Checkbox
  label="Select all items"
  indeterminate={someItemsSelected && !allItemsSelected}
  checked={allItemsSelected}
  onChange={handleSelectAll}
/>

// Checkbox group
{options.map(option => (
  <Checkbox
    key={option.id}
    label={option.label}
    checked={selectedOptions.includes(option.id)}
    onChange={(checked) => {
      if (checked) {
        setSelectedOptions([...selectedOptions, option.id]);
      } else {
        setSelectedOptions(selectedOptions.filter(id => id !== option.id));
      }
    }}
  />
))}
```

## 📊 Data Display Components

### Table

A flexible table component with sorting, filtering, and pagination.

**Props:**
```typescript
interface TableProps {
  data: any[];
  columns: TableColumn[];
  sortable?: boolean;
  filterable?: boolean;
  pagination?: {
    page: number;
    pageSize: number;
    total: number;
    onPageChange: (page: number) => void;
  };
  loading?: boolean;
  emptyMessage?: string;
  className?: string;
}

interface TableColumn {
  key: string;
  title: string;
  sortable?: boolean;
  filterable?: boolean;
  render?: (value: any, row: any) => React.ReactNode;
  width?: string;
}
```

**Examples:**
```jsx
import { Table } from 'your-component-library';

const users = [
  { id: 1, name: 'John Doe', email: 'john@example.com', role: 'Admin' },
  { id: 2, name: 'Jane Smith', email: 'jane@example.com', role: 'User' },
  { id: 3, name: 'Bob Johnson', email: 'bob@example.com', role: 'User' }
];

const columns = [
  { key: 'id', title: 'ID', width: '80px' },
  { key: 'name', title: 'Name', sortable: true },
  { key: 'email', title: 'Email', sortable: true },
  { 
    key: 'role', 
    title: 'Role',
    render: (role) => (
      <Badge variant={role === 'Admin' ? 'primary' : 'secondary'}>
        {role}
      </Badge>
    )
  },
  {
    key: 'actions',
    title: 'Actions',
    render: (_, user) => (
      <Flex gap="8px">
        <Button size="sm" variant="outline">Edit</Button>
        <Button size="sm" variant="danger">Delete</Button>
      </Flex>
    )
  }
];

// Basic table
<Table data={users} columns={columns} />

// Table with sorting and pagination
<Table
  data={users}
  columns={columns}
  sortable
  pagination={{
    page: currentPage,
    pageSize: 10,
    total: totalUsers,
    onPageChange: setCurrentPage
  }}
/>
```

---

### Badge

A small status indicator component.

**Props:**
```typescript
interface BadgeProps {
  variant?: 'primary' | 'secondary' | 'success' | 'warning' | 'error' | 'info';
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  className?: string;
}
```

**Examples:**
```jsx
import { Badge } from 'your-component-library';

// Basic badges
<Badge>Default</Badge>
<Badge variant="primary">Primary</Badge>
<Badge variant="success">Success</Badge>
<Badge variant="warning">Warning</Badge>
<Badge variant="error">Error</Badge>

// Different sizes
<Badge size="sm">Small</Badge>
<Badge size="md">Medium</Badge>
<Badge size="lg">Large</Badge>

// Status indicators
<Badge variant="success">Active</Badge>
<Badge variant="warning">Pending</Badge>
<Badge variant="error">Inactive</Badge>
```

## 🎮 Interactive Components

### Tabs

A tab navigation component for organizing content.

**Props:**
```typescript
interface TabsProps {
  defaultTab?: string;
  activeTab?: string;
  onTabChange?: (tabId: string) => void;
  variant?: 'default' | 'pills' | 'underline';
  children: React.ReactNode;
  className?: string;
}

interface TabProps {
  id: string;
  label: string;
  disabled?: boolean;
  children: React.ReactNode;
}
```

**Examples:**
```jsx
import { Tabs, Tab } from 'your-component-library';

// Basic tabs
<Tabs defaultTab="profile">
  <Tab id="profile" label="Profile">
    <h3>Profile Settings</h3>
    <p>Manage your profile information.</p>
  </Tab>
  <Tab id="security" label="Security">
    <h3>Security Settings</h3>
    <p>Manage your password and security options.</p>
  </Tab>
  <Tab id="notifications" label="Notifications">
    <h3>Notification Preferences</h3>
    <p>Choose how you want to be notified.</p>
  </Tab>
</Tabs>

// Controlled tabs
<Tabs 
  activeTab={currentTab} 
  onTabChange={setCurrentTab}
  variant="pills"
>
  <Tab id="overview" label="Overview">
    Dashboard overview content
  </Tab>
  <Tab id="analytics" label="Analytics">
    Analytics and reporting content
  </Tab>
  <Tab id="settings" label="Settings" disabled>
    Settings content (disabled)
  </Tab>
</Tabs>
```

---

### Accordion

An accordion component for collapsible content sections.

**Props:**
```typescript
interface AccordionProps {
  multiple?: boolean;
  defaultOpen?: string[];
  children: React.ReactNode;
  className?: string;
}

interface AccordionItemProps {
  id: string;
  title: string;
  disabled?: boolean;
  children: React.ReactNode;
}
```

**Examples:**
```jsx
import { Accordion, AccordionItem } from 'your-component-library';

// Basic accordion
<Accordion>
  <AccordionItem id="faq1" title="What is your return policy?">
    <p>We offer a 30-day return policy for all items in original condition.</p>
  </AccordionItem>
  <AccordionItem id="faq2" title="How long does shipping take?">
    <p>Standard shipping takes 3-5 business days. Express shipping is available.</p>
  </AccordionItem>
  <AccordionItem id="faq3" title="Do you ship internationally?">
    <p>Yes, we ship to most countries worldwide. Shipping costs vary by location.</p>
  </AccordionItem>
</Accordion>

// Multiple items open
<Accordion multiple defaultOpen={['section1', 'section3']}>
  <AccordionItem id="section1" title="Section 1">
    Content for section 1
  </AccordionItem>
  <AccordionItem id="section2" title="Section 2">
    Content for section 2
  </AccordionItem>
  <AccordionItem id="section3" title="Section 3">
    Content for section 3
  </AccordionItem>
</Accordion>
```

## 🎨 Styling and Theming

### Theme Provider

Wrap your app with the theme provider to enable theming.

```jsx
import { ThemeProvider, createTheme } from 'your-component-library';

const theme = createTheme({
  colors: {
    primary: '#007bff',
    secondary: '#6c757d',
    success: '#28a745',
    warning: '#ffc107',
    error: '#dc3545',
    info: '#17a2b8'
  },
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
    lg: '24px',
    xl: '32px'
  },
  borderRadius: {
    sm: '4px',
    md: '8px',
    lg: '12px'
  }
});

function App() {
  return (
    <ThemeProvider theme={theme}>
      <YourApp />
    </ThemeProvider>
  );
}
```

### CSS Variables

Components use CSS variables for easy customization:

```css
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --border-radius: 8px;
  --spacing-md: 16px;
  --font-family: 'Inter', sans-serif;
}

/* Dark theme */
[data-theme="dark"] {
  --primary-color: #0d6efd;
  --background-color: #1a1a1a;
  --text-color: #ffffff;
}
```

## ♿ Accessibility

All components follow WCAG 2.1 guidelines and include:

- **Keyboard Navigation**: Full keyboard support with logical tab order
- **Screen Reader Support**: Proper ARIA labels and descriptions
- **Focus Management**: Visible focus indicators and focus trapping in modals
- **Color Contrast**: AA compliant color ratios
- **Semantic HTML**: Proper use of semantic elements

### Accessibility Examples

```jsx
// Button with proper ARIA attributes
<Button 
  aria-label="Save document" 
  aria-describedby="save-help"
>
  Save
</Button>
<div id="save-help">Saves the current document to your account</div>

// Form with proper labeling
<Form>
  <Input 
    label="Email"
    required
    aria-describedby="email-error"
    error={emailError}
  />
  {emailError && (
    <div id="email-error" role="alert" aria-live="polite">
      {emailError}
    </div>
  )}
</Form>

// Modal with focus management
<Modal
  isOpen={isOpen}
  onClose={onClose}
  aria-labelledby="modal-title"
  aria-describedby="modal-description"
>
  <h2 id="modal-title">Confirm Action</h2>
  <p id="modal-description">This action cannot be undone.</p>
</Modal>
```

## ⚡ Performance

### Code Splitting

Components support tree shaking and can be imported individually:

```javascript
// Import only what you need
import { Button } from 'your-component-library/Button';
import { Modal } from 'your-component-library/Modal';

// Or use named imports (with proper tree shaking)
import { Button, Modal } from 'your-component-library';
```

### Bundle Size

| Component | Gzipped Size | Dependencies |
|-----------|--------------|--------------|
| Button | 2.1kb | None |
| Input | 3.2kb | None |
| Modal | 4.5kb | Portal, FocusLock |
| Table | 8.1kb | Virtualization |
| DatePicker | 12.3kb | Date utilities |

### Performance Tips

```jsx
// Use React.memo for expensive components
const ExpensiveComponent = React.memo(({ data }) => {
  // Expensive rendering logic
  return <ComplexVisualization data={data} />;
});

// Lazy load heavy components
const DataTable = React.lazy(() => import('./DataTable'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <DataTable data={data} />
    </Suspense>
  );
}

// Virtualize large lists
<Table
  data={largeDataset}
  virtualized
  rowHeight={50}
  maxHeight={400}
/>
```

## 🧪 Testing

### Testing Examples

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from 'your-component-library';

describe('Button', () => {
  test('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  test('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  test('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
});
```

### Storybook Integration

```jsx
// Button.stories.js
export default {
  title: 'Components/Button',
  component: Button,
  argTypes: {
    variant: {
      control: { type: 'select' },
      options: ['primary', 'secondary', 'outline', 'ghost', 'danger']
    }
  }
};

export const Primary = {
  args: {
    variant: 'primary',
    children: 'Primary Button'
  }
};

export const WithIcon = {
  args: {
    icon: <SaveIcon />,
    children: 'Save'
  }
};
```

---

**Need Help?** Check our [Component Usage Guide](../guides/components.md) or [Storybook](https://storybook.your-domain.com) for interactive examples.