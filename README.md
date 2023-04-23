# Assignment
#1.Explain what the simple List component does.
- The simple List component is a React functional component that takes an array of items as a prop and renders them as a list of clickable items. 
When an item is clicked, its background color changes to green and stays that way until another item is clicked.

- The List component consists of two other components - SingleListItem and ListComponent. The SingleListItem component is a functional component that
 renders a single list item with the item's text and background color based on whether it's selected or not.

- The ListComponent component maps over the array of items passed to it and renders a SingleListItem component for each item. It also manages the 
state of which item is currently selected using the useState hook. Whenever the array of items changes, the ListComponent resets the selected index
 to 0 using the useEffect hook.

- The handleClick function is used to update the selected index whenever a list item is clicked. It takes the index of the clicked item as an 
argument and sets the selected index state accordingly.

Finally, the List component exports the ListComponent component wrapped in the memo higher-order component to memoize its rendering, which can 
improve performance in some cases.

#2.What problems / warnings are there with code?

There are a few problems with the code:

The setSelectedIndex function should be declared using useState, like this

const [selectedIndex, setSelectedIndex] = useState(null);

The current code declares setSelectedIndex as the setter function for selectedIndex, which is not correct.

The propTypes definition for the items prop in the WrappedListComponent component is not correct. Instead of array(PropTypes.shapeOf(...)), it 
should be PropTypes.arrayOf(PropTypes.shape(...)).

In the SingleListItem component, the isSelected prop is being passed as a boolean, but in the component itself it's being used to set the 
background color of the li element. This will result in the background color being set to "green" when isSelected is truthy (which includes true, 
non-empty strings, and non-zero numbers) and "red" otherwise. To fix this, the isSelected prop should be compared to the index of the current item,
 like this:
 
style={{ backgroundColor: isSelected === index ? 'green' : 'red' }}

The onClickHandler prop in the SingleListItem component is being called immediately when the component is rendered, instead of being passed as a
 function to the onClick handler. To fix this, the onClick handler should be wrapped in an arrow function, like this:
onClick={() => onClickHandler(index)}

#3.Please fix, optimize, and/or modify the component as much as you think is necessary.
  There are a few potential optimizations that could be made to this code:

Use useCallback to memoize the handleClick function. This function is recreated on every render, which can be inefficient if the component is 
rerendered frequently. Using useCallback can memoize the function and prevent it from being recreated unnecessarily.

Use key prop on the SingleListItem component to help React optimize the rendering of the list. React uses the key prop to determine which items 
have changed, added or removed, which can be useful when rendering large lists.

Change the useState initialization for setSelectedIndex to a default value instead of null. This can improve the performance of the component by 
reducing the number of times the state needs to be updated.
  
import React, { useState, useEffect, memo, useCallback } from 'react';
import PropTypes from 'prop-types';

// Single List Item
const WrappedSingleListItem = ({
  index,
  isSelected,
  onClickHandler,
  text,
}) => {
  return (
    <li
      style={{ backgroundColor: isSelected ? 'green' : 'red'}}
      onClick={onClickHandler}
    >
      {text}
    </li>
  );
};

WrappedSingleListItem.propTypes = {
  index: PropTypes.number,
  isSelected: PropTypes.bool,
  onClickHandler: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired,
};

const SingleListItem = memo(WrappedSingleListItem);

// List Component
const WrappedListComponent = ({
  items,
}) => {
  const [selectedIndex, setSelectedIndex] = useState(0);

  useEffect(() => {
    setSelectedIndex(0);
  }, [items]);

  const handleClick = useCallback(index => {
    setSelectedIndex(index);
  }, []);

  return (
    <ul style={{ textAlign: 'left' }}>
      {items.map((item, index) => (
        <SingleListItem
          key={item.text}
          onClickHandler={() => handleClick(index)}
          text={item.text}
          index={index}
          isSelected={index === selectedIndex}
        />
      ))}
    </ul>
  )
};

WrappedListComponent.propTypes = {
  items: PropTypes.arrayOf(PropTypes.shape({
    text: PropTypes.string.isRequired,
  })),
};

WrappedListComponent.defaultProps = {
  items: [],
};

const List = memo(WrappedListComponent);

export default List;
